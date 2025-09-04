**SLA (Service Level Agreement) metrics** for testing projects are measurable indicators that define the expected quality, timeliness, and effectiveness of testing services provided by a QA/testing team to its clients or stakeholders. These metrics help ensure that the testing process meets agreed-upon standards and business requirements.

### Common SLA Metrics for Testing Projects

1. **Test Case Execution Rate**
   - **Definition:** Percentage of planned test cases executed within the specified timeline.
   - **Formula:** (Number of test cases executed / Total planned test cases) × 100

2. **Defect Detection Rate**
   - **Definition:** Percentage of defects detected during a testing cycle.
   - **Formula:** (Number of defects detected / Total number of defects found post-release + during testing) × 100

3. **Defect Leakage**
   - **Definition:** Number or percentage of defects missed during testing and found in production.
   - **Formula:** (Defects found in production / Total defects found) × 100

4. **Test Coverage**
   - **Definition:** Percentage of application requirements or code covered by test cases.
   - **Formula:** (Requirements covered by test cases / Total requirements) × 100

5. **Test Execution Timeliness**
   - **Definition:** Percentage of test cycles completed within the agreed schedule.
   - **Formula:** (Number of test cycles completed on time / Total test cycles) × 100

6. **Defect Resolution Time**
   - **Definition:** Average time taken to resolve (fix and close) a defect.
   - **Formula:** (Total time to resolve all defects / Number of defects resolved)

7. **Test Environment Availability**
   - **Definition:** Amount of time the test environment is available for use as per the schedule.
   - **Formula:** (Actual environment uptime / Scheduled uptime) × 100

8. **First Time Pass Rate**
   - **Definition:** Percentage of test cases that pass on the first execution, without requiring rework.
   - **Formula:** (Number of test cases passed on first attempt / Total executed test cases) × 100

9. **Reopen Rate**
   - **Definition:** Percentage of defects that are reopened after being closed.
   - **Formula:** (Number of defects reopened / Total defects closed) × 100

10. **Customer/Stakeholder Satisfaction**
    - **Definition:** Qualitative or quantitative measure (e.g., survey score) of how satisfied stakeholders are with the QA process and deliverables.


### Example SLA Metrics

| Metric                     | Target Value | Calculation                                           |
|----------------------------|--------------|-------------------------------------------------------|
| Test Case Execution Rate   | ≥ 95%        | (Executed / Planned) × 100                            |
| Defect Detection Rate      | ≥ 85%        | (Detected during testing / Total defects) × 100       |
| Defect Leakage             | ≤ 2%         | (Prod defects / Total defects) × 100                  |
| Test Coverage              | ≥ 90%        | (Covered requirements / Total requirements) × 100     |
| Test Execution Timeliness  | ≥ 98%        | (On-time cycles / Total cycles) × 100                 |
| Defect Resolution Time     | ≤ 3 days     | (Total resolution time / Defects resolved)            |
| Test Environment Availability | ≥ 99%     | (Uptime / Scheduled time) × 100                       |
| First Time Pass Rate       | ≥ 85%        | (Passed first attempt / Total executed) × 100         |
| Reopen Rate                | ≤ 5%         | (Reopened defects / Closed defects) × 100             |
| Stakeholder Satisfaction   | ≥ 4/5        | (Survey or feedback score)                            |

 
SLA metrics should be agreed upon between the QA team and stakeholders at the start of the project and reviewed regularly for continuous improvement. They serve as a baseline for measuring and improving the quality and efficiency of testing services.


### API Test Metrics SLA

**typical target values/benchmarks for API testing metrics**, based on industry best practices and quality expectations:

| Metric                        | Target/Benchmark           | Notes/Comments                                  |
|-------------------------------|----------------------------|-------------------------------------------------|
| **Endpoint Coverage**         | 100%                       | All production endpoints should be tested       |
| **Method Coverage**           | 100%                       | All supported HTTP methods per endpoint         |
| **Parameter Coverage**        | >80%                       | High coverage for input combinations            |
| **Number of Tests Executed**  | No fixed target            | Should increase with API complexity             |
| **Pass/Fail Rate**            | >95% pass                  | Indicates overall quality, aim for near 100%    |
| **Defect Density**            | <0.5 defects/endpoint/1kLoC| Lower is better; ideally zero                   |
| **Response Time (Avg)**       | <500ms                     | Varies by business needs, <200ms is optimal     |
| **Throughput**                | Context-specific           | Set based on API type, e.g., >1000 req/sec      |
| **Error Rate**                | <1%                        | Minimize errors; <0.1% for critical APIs        |
| **Latency**                   | <200ms                     | Lower latency is better for user experience     |
| **Uptime/Downtime**           | >99.9%                     | High availability; align with SLA               |
| **MTBF**                      | As high as possible        | Higher = more reliability                       |
| **MTTR**                      | As low as possible         | Lower = faster recovery from failures           |
| **Vulnerability Detection**   | 0                          | No vulnerabilities ideally                      |
| **Auth Coverage**             | 100%                       | All auth scenarios tested                       |
| **Regression Defects**        | 0                          | No new/recurring defects after updates          |
| **Automation Rate**           | >80%                       | Higher automation increases efficiency          |
| **Build Integration**         | Context-specific           | Frequent test runs per CI/CD cycle              |
| **Data Validity**             | 100%                       | All responses must be correct/valid             |
| **Schema Validation Errors**  | 0                          | All responses conform to schema                 |
| **Documentation Coverage**    | 100%                       | All endpoints documented and up-to-date         |
| **Documentation Defects**     | 0                          | No issues in API documentation                  |

**Notes:**
- “Context-specific” means the target should be agreed based on your API’s business requirements, expected load, and SLAs.
- MTBF (Mean Time Between Failures) and MTTR (Mean Time to Recovery) targets depend on reliability goals; strive for high MTBF and low MTTR.
- Number of tests executed and build integration frequency should grow with product maturity and CI/CD adoption.
