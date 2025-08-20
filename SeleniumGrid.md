Selenium grid host name validators:
-------------------------------------

To find if Selenium Grid has a node of a specific configuration (e.g., browser version, platform, capabilities) in Selenium Java, you generally need to query the Grid’s status endpoint (available in Selenium Grid 4+) and inspect the returned information.

Steps1. Call the Selenium Grid status endpoint
-----------------------------------------------
	http://<grid-host>:<port>/status

Step2. Parse the JSON and check for your required configuration
-----------------------------------------------------------------


Example Code: Check for Chrome Node on Windows

import java.io.*;
import java.net.*;
import org.json.*;

public class GridNodeChecker {

    public static boolean hasNodeWithConfig(String gridUrl, String browserName, String platformName) throws Exception {
        URL url = new URL(gridUrl + "/status");
        HttpURLConnection con = (HttpURLConnection) url.openConnection();
        con.setRequestMethod("GET");
        BufferedReader in = new BufferedReader(new InputStreamReader(con.getInputStream()));
        StringBuilder response = new StringBuilder();
        String inputLine;
        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }
        in.close();

        JSONObject json = new JSONObject(response.toString());
        JSONArray nodes = json.getJSONObject("value").getJSONArray("nodes");

        for (int i = 0; i < nodes.length(); i++) {
            JSONObject node = nodes.getJSONObject(i);
            JSONArray slots = node.getJSONArray("slots");
            for (int j = 0; j < slots.length(); j++) {
                JSONObject slot = slots.getJSONObject(j);
                JSONObject capabilities = slot.getJSONObject("stereotype");
                String nodeBrowser = capabilities.optString("browserName");
                String nodePlatform = capabilities.optString("platformName");
                if (browserName.equalsIgnoreCase(nodeBrowser) &&
                    platformName.equalsIgnoreCase(nodePlatform)) {
                    return true;
                }
            }
        }
        return false;
    }

    public static void main(String[] args) throws Exception {
        String gridUrl = "http://localhost:4444"; // update as needed
        boolean hasChromeWindows = hasNodeWithConfig(gridUrl, "chrome", "windows");
        System.out.println("Has Chrome on Windows node: " + hasChromeWindows);
    }
}


What to Look For in the Capabilities
	"browserName" (e.g., "chrome", "firefox")
	"platformName" (e.g., "windows", "linux", "mac" or "any")
	You can also check for "browserVersion", "maxInstances", etc.



# sample API

https://github.com/RationaleEmotions/talk2grid

	to find out the node to which a test was routed to ?
	to route all traffic directly to the node for browser interactions ?
	to upload files to a remote machine (Selenium node) on which the current Selenium test is running

https://rationaleemotions.wordpress.com/2016/01/15/where-did-my-test-run/
	to find out the node to which a test was routed to ?

Step1: utility class 
-=-=-=-=-=-=-=-=-=-=-=

import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import org.apache.http.HttpEntity;
import org.apache.http.HttpHost;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.message.BasicHttpEntityEnclosingRequest;
import org.openqa.selenium.remote.SessionId;
 
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.logging.Level;
import java.util.logging.Logger;
 
/**
 * This demo code was written using Selenium v2.49.0
 */
public class ActiveNodeDeterminer {
    private String gridHostName;
    private int gridPort;
 
    private static final Logger LOGGER = Logger.getLogger(ActiveNodeDeterminer.class.getCanonicalName());
 
    /**
     *
     * @param gridHostName - The host where the Grid Hub is running.
     * @param gridPort - The port on which the Grid port is listening to.
     */
    public ActiveNodeDeterminer(String gridHostName, int gridPort) {
        this.gridHostName = gridHostName;
        this.gridPort = gridPort;
    }
 
    /**
     * @param sessionId - A {@link SessionId} object that represents a valid session.
     * @return - A {@link GridNode} object that represents the node to which the session was routed to.
     */
    public GridNode getNodeInfoForSession(SessionId sessionId) {
        GridNode node = null;
        CloseableHttpClient client = HttpClientBuilder.create().build();
        CloseableHttpResponse response = null;
        try {
            URL url = new URL("http://" + gridHostName + ":" + gridPort + "/grid/api/testsession?session=" + sessionId);
            BasicHttpEntityEnclosingRequest r = new BasicHttpEntityEnclosingRequest("POST", url.toExternalForm());
            response = client.execute(new HttpHost(gridHostName, gridPort), r);
            JsonObject object = extractJson(response.getEntity());
            URL tempUrl = new URL(object.get("proxyId").getAsString());
            node = new GridNode(tempUrl.getHost(), tempUrl.getPort());
        } catch (Exception e) {
            String errorMsg = "Failed to acquire remote webdriver node and port info. Root cause: " + e.getMessage();
            LOGGER.log(Level.SEVERE, errorMsg, e);
            throw new RuntimeException(errorMsg, e);
        } finally {
            try {
                if (response != null) {
                    response.close();
                }
            } catch (IOException e) {
                LOGGER.log(Level.SEVERE, e.getMessage(), e);
            }
        }
        LOGGER.info("Session " + sessionId + " was routed to " + node.toString());
        return node;
    }
 
    private JsonObject extractJson(HttpEntity entity) throws IOException {
        try (BufferedReader br = new BufferedReader(new InputStreamReader(entity.getContent()))) {
            StringBuilder builder = new StringBuilder();
            String line;
            while ((line = br.readLine()) != null) {
                builder.append(line);
            }
            return new JsonParser().parse(builder.toString()).getAsJsonObject();
        }
    }
 
    /**
     * A simple POJO (Plain Old Java Object) class that represents a Node in the
     * Selenium Grid environment.
     */
    public static class GridNode {
        private String nodeIp;
        private int nodePort;
 
 
        public GridNode(String nodeIp, int nodePort) {
            this.nodeIp = nodeIp;
            this.nodePort = nodePort;
        }
 
        /**
         * @return - A String that represents the IP Address of the node.
         */
        public String getNodeIp() {
            return this.nodeIp;
        }
 
        /**
         * @return - An int that represents the port number of the node.
         */
        public int getNodePort() {
            return this.nodePort;
        }
 
        @Override
        public String toString() {
            return "GridNode [IP='" + nodeIp + "', Port=" + nodePort + "]";
        }
    }
}

Step 2: TestNG test client
-=-=-=-=-=-=-=-=-=-=-=-=-=-=

package organized.chaos.testng;
 
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.remote.RemoteWebDriver;
import org.testng.Reporter;
import org.testng.annotations.AfterClass;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;
import organized.chaos.webdriver.ActiveNodeDeterminer;
 
import java.net.URL;
 
public class SampleTest {
    private static final String IP = "localhost";
    private static final int PORT = 4444;
 
    private RemoteWebDriver driver;
    private ActiveNodeDeterminer determiner = new ActiveNodeDeterminer(IP, PORT);
 
    @BeforeClass
    public void setup() throws Exception {
        URL url = new URL("http://" + IP + ":" + PORT + "/wd/hub");
        driver = new RemoteWebDriver(url, DesiredCapabilities.firefox());
    }
 
    @Test
    public void test() {
        Reporter.log("Node : " + determiner.getNodeInfoForSession(driver.getSessionId()), true);
    }
 
    @AfterClass
    public void tearDown() {
        if (driver != null) {
            driver.quit();
        }
    }
}


Q2. https://rationaleemotions.wordpress.com/2017/03/19/routing-tests-directly-to-selenium-nodes/
Q3. https://rationaleemotions.github.io/gridopadesham/
Q4. https://www.selenium.dev/documentation/grid/configuration/cli_options/





