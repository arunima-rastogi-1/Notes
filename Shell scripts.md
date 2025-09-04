## script checks all remote Git branches except those containing "release" or "master" in their names.
```
branches=`git branch -r | grep -v "release\|master"`

today=`date +%s`
weekAgo=`expr $today - 60 \* 60 \* 24 \* 7`
exitCode=0

for branch in $branches
do
  comitter=`git show -s --format='%cn' $branch`
  date=`git show -s --format='%ct' $branch`
  formattedDate=`date -d @$date`
  if [ $date -le $weekAgo  ] ; then 
    echo "$formattedDate \t $comitter \t $branch"
    exitCode=1
  fi
done

exit $exitCode
```

## The script is a Maven build automation wrapper for a Java (probably Spring Boot) project.
It performs (in order):

	Clean (delete old build artifacts)
	Unit Testing
	Packaging (build a deployable jar/war)
	with options to skip or modify each step using command-line flags.


````
#!/bin/bash
set -e

BASE_DIR="$(cd "$(dirname "$0")"; pwd)";

RELEASE="${RELEASE:-1.0.0-SNAPSHOT}";
MAVEN_FLAGS=""

if [[ " $* " = *" --no-colour "* ]]; then
  MAVEN_FLAGS="${MAVEN_FLAGS} -B";
fi;

# Clean
if ! [[ " $* " = *" --skip-clean "* ]]; then
  mvn clean \
    ${MAVEN_FLAGS} \
    -Drevision="$RELEASE" \
    -f "$BASE_DIR/pom.xml";
fi;

# Unit Test
if ! [[ " $* " = *" --skip-tests "* || " $* " = *" --skip-unit-tests "* ]]; then
  echo "============ RUNNING UNIT TESTS";

  mvn test \
    ${MAVEN_FLAGS} \
    -Drevision="$RELEASE" \
    -f "$BASE_DIR/pom.xml";
fi;

if ! [[ " $* " = *" --skip-package "* ]]; then
  echo "============ RUNNING PACKAGE";

  # Package as Spring Boot app
  mvn package spring-boot:repackage \
    ${MAVEN_FLAGS} \
    -Drevision="$RELEASE" \
    -DskipTests \
    -f "$BASE_DIR/pom.xml";
fi;

echo "All stages succeeded";

````


