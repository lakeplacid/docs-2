% Using Oracle as a database backend

<!---
Original
http://support.rundeck.com/customer/en/portal/articles/2415681-oracle-setup)
--->

* Download the latest oracle driver (eg, ojdbc7.jar version 12.1.0.2): 

[http://www.oracle.com/technetwork/database/features/jdbc/default-2280470.html](http://www.oracle.com/technetwork/database/features/jdbc/default-2280470.html)

* Copy the downloaded file `ojdbc7.jar` to the `$CATALINA_HOME/lib`
* Update `$RDECK_BASE/etc/rundeck-config.properties`:

```
rundeck.projectsStorageType=db

dataSource.url = jdbc:oracle:thin:@127.0.0.1:1521:orcl # (change server name and instance name)
dataSource.driverClassName = oracle.jdbc.driver.OracleDriver
dataSource.username = XXXXXX
dataSource.password = XXXXXXX
dataSource.dialect = org.hibernate.dialect.Oracle10gDialect
```

* Database’s tables must be created manually ( to avoid issue https://github.com/rundeck/rundeck/issues/676) .

The database user creating these tables should have the following permissions in order to create all needed database objects:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.sql}
grant create session to username;
grant create sequence to username;
grant create table to username;
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This is the database setup script:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.sql}
CREATE TABLE AUTH_TOKEN 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
AUTH_ROLES CLOB, 
TOKEN VARCHAR2(255 CHAR), 
USER_ID NUMBER(19,0), 
CREATOR VARCHAR2(255 CHAR), 
DATE_CREATED TIMESTAMP (6), 
EXPIRATION TIMESTAMP (6), 
LAST_UPDATED TIMESTAMP (6), 
UUID VARCHAR2(255 CHAR)
)
/
ALTER TABLE AUTH_TOKEN
ADD CONSTRAINT SYS_C0011702
PRIMARY KEY (ID)   
/
ALTER TABLE AUTH_TOKEN
ADD CONSTRAINT UK_PL6HLEYEX3OFOAKLDUHBYRETW
UNIQUE (TOKEN)   
/
 
CREATE TABLE BASE_REPORT 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
ACTION_TYPE VARCHAR2(256 CHAR), 
AUTHOR VARCHAR2(255 CHAR), 
CTX_NAME VARCHAR2(255 CHAR), 
CTX_PROJECT VARCHAR2(255 CHAR), 
CTX_TYPE VARCHAR2(255 CHAR), 
DATE_COMPLETED TIMESTAMP (6), 
DATE_STARTED TIMESTAMP (6), 
MAPREF_URI VARCHAR2(255 CHAR), 
MESSAGE CLOB, 
NODE VARCHAR2(255 CHAR), 
REPORT_ID VARCHAR2(3072 CHAR), 
STATUS VARCHAR2(256 CHAR), 
TAGS VARCHAR2(255 CHAR), 
TITLE CLOB, 
CLASS VARCHAR2(255 CHAR), 
ABORTED_BY_USER VARCHAR2(255 CHAR), 
ADHOC_EXECUTION NUMBER(1,0), 
ADHOC_SCRIPT CLOB, 
CTX_COMMAND VARCHAR2(255 CHAR), 
CTX_CONTROLLER VARCHAR2(255 CHAR), 
JC_EXEC_ID VARCHAR2(255 CHAR), 
JC_JOB_ID VARCHAR2(255 CHAR), 
FAILED_NODE_LIST CLOB, 
FILTER_APPLIED CLOB, 
SUCCEEDED_NODE_LIST CLOB
)
/
ALTER TABLE BASE_REPORT
ADD CONSTRAINT SYS_C0011778
PRIMARY KEY (ID)
/
 
CREATE TABLE EXECUTION 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
ABORTEDBY VARCHAR2(255 CHAR), 
ARG_STRING CLOB, 
CANCELLED NUMBER(1,0), 
DATE_COMPLETED TIMESTAMP (6), 
DATE_STARTED TIMESTAMP (6), 
DO_NODEDISPATCH NUMBER(1,0), 
FAILED_NODE_LIST CLOB, 
FILTER CLOB, 
LOGLEVEL VARCHAR2(255 CHAR), 
NODE_EXCLUDE CLOB, 
NODE_EXCLUDE_NAME CLOB, 
NODE_EXCLUDE_OS_ARCH CLOB, 
NODE_EXCLUDE_OS_FAMILY CLOB, 
NODE_EXCLUDE_OS_NAME CLOB, 
NODE_EXCLUDE_OS_VERSION CLOB, 
NODE_EXCLUDE_PRECEDENCE NUMBER(1,0), 
NODE_EXCLUDE_TAGS CLOB, 
NODE_INCLUDE CLOB, 
NODE_INCLUDE_NAME CLOB, 
NODE_INCLUDE_OS_ARCH CLOB, 
NODE_INCLUDE_OS_FAMILY CLOB, 
NODE_INCLUDE_OS_NAME CLOB, 
NODE_INCLUDE_OS_VERSION CLOB, 
NODE_INCLUDE_TAGS CLOB, 
NODE_KEEPGOING NUMBER(1,0), 
NODE_RANK_ATTRIBUTE VARCHAR2(255 CHAR), 
NODE_RANK_ORDER_ASCENDING NUMBER(1,0), 
NODE_THREADCOUNT NUMBER(10,0), 
OUTPUTFILEPATH CLOB, 
PROJECT VARCHAR2(255 CHAR), 
RETRY CLOB, 
RETRY_ATTEMPT NUMBER(10,0), 
RETRY_EXECUTION_ID NUMBER(19,0), 
SCHEDULED_EXECUTION_ID NUMBER(19,0), 
SERVER_NODEUUID VARCHAR2(36 CHAR), 
STATUS VARCHAR2(255 CHAR), 
SUCCEEDED_NODE_LIST CLOB, 
TIMED_OUT NUMBER(1,0), 
TIMEOUT CLOB, 
RDUSER VARCHAR2(255 CHAR), 
WILL_RETRY NUMBER(1,0), 
WORKFLOW_ID NUMBER(19,0), 
ORCHESTRATOR_ID NUMBER(19,0), 
EXECUTION_TYPE VARCHAR2(30 CHAR), 
NODE_FILTER_EDITABLE NUMBER(1,0), 
USER_ROLE_LIST CLOB
) 
/
ALTER TABLE EXECUTION
ADD CONSTRAINT SYS_C0011784
PRIMARY KEY (ID)   
/
 
CREATE TABLE JOB_FILE_RECORD 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
DATE_CREATED TIMESTAMP (6), 
EXECUTION_ID NUMBER(19,0), 
EXPIRATION_DATE TIMESTAMP (6), 
FILE_NAME VARCHAR2(1024 CHAR), 
FILE_STATE VARCHAR2(255 CHAR), 
JOB_ID VARCHAR2(255 CHAR), 
LAST_UPDATED TIMESTAMP (6), 
PROJECT VARCHAR2(255 CHAR), 
RECORD_NAME VARCHAR2(255 CHAR), 
RECORD_TYPE VARCHAR2(255 CHAR), 
SERVER_NODEUUID VARCHAR2(36 CHAR),
SHA VARCHAR2(64 CHAR), 
"SIZE" NUMBER(19,0), 
STORAGE_META CLOB, 
STORAGE_REFERENCE CLOB, 
STORAGE_TYPE VARCHAR2(255 CHAR), 
RDUSER VARCHAR2(255 CHAR), 
UUID VARCHAR2(255 CHAR)
) 
/
ALTER TABLE JOB_FILE_RECORD
ADD CONSTRAINT SYS_C0011133
PRIMARY KEY (ID)   
/
 
 
CREATE TABLE LOG_FILE_STORAGE_REQUEST 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
COMPLETED NUMBER(1,0), 
DATE_CREATED TIMESTAMP (6), 
EXECUTION_ID NUMBER(19,0), 
FILETYPE VARCHAR2(255 CHAR), 
LAST_UPDATED TIMESTAMP (6), 
PLUGIN_NAME VARCHAR2(255 CHAR)
) 
/
ALTER TABLE LOG_FILE_STORAGE_REQUEST
ADD CONSTRAINT SYS_C0011710
PRIMARY KEY (ID)
/
 
CREATE TABLE MESSAGING 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
BODY CLOB, 
DATE_CREATED TIMESTAMP (6), 
LAST_UPDATED TIMESTAMP (6), 
NAMESPACE VARCHAR2(255 BYTE) DEFAULT NULL, 
SENDER VARCHAR2(255 BYTE), 
STATE VARCHAR2(255 BYTE), 
TOPIC VARCHAR2(255 BYTE), 
TYPE VARCHAR2(255 BYTE), 
DESTINATION VARCHAR2(255 BYTE) DEFAULT NULL
) 
/
ALTER TABLE MESSAGING
ADD CONSTRAINT MESSAGING_PK
PRIMARY KEY (ID)
/
 
CREATE TABLE NODE_FILTER 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
FILTER CLOB, 
NAME VARCHAR2(255 CHAR), 
NODE_EXCLUDE CLOB, 
NODE_EXCLUDE_NAME CLOB, 
NODE_EXCLUDE_OS_ARCH CLOB, 
NODE_EXCLUDE_OS_FAMILY CLOB, 
NODE_EXCLUDE_OS_NAME CLOB, 
NODE_EXCLUDE_OS_VERSION CLOB, 
NODE_EXCLUDE_PRECEDENCE NUMBER(1,0), 
NODE_EXCLUDE_TAGS CLOB, 
NODE_INCLUDE CLOB, 
NODE_INCLUDE_NAME CLOB, 
NODE_INCLUDE_OS_ARCH CLOB, 
NODE_INCLUDE_OS_FAMILY CLOB, 
NODE_INCLUDE_OS_NAME CLOB, 
NODE_INCLUDE_OS_VERSION CLOB, 
NODE_INCLUDE_TAGS CLOB, 
PROJECT VARCHAR2(255 CHAR), 
USER_ID NUMBER(19,0)
)
/
ALTER TABLE NODE_FILTER
ADD CONSTRAINT SYS_C0011789
PRIMARY KEY (ID)    
/
 
CREATE TABLE NOTIFICATION 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
CONTENT CLOB, 
EVENT_TRIGGER VARCHAR2(255 CHAR), 
SCHEDULED_EXECUTION_ID NUMBER(19,0), 
TYPE VARCHAR2(255 CHAR)
) 
/
ALTER TABLE NOTIFICATION
ADD CONSTRAINT SYS_C0011716
PRIMARY KEY (ID)
/
 
CREATE TABLE ORCHESTRATOR 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
CONTENT CLOB, 
TYPE VARCHAR2(255 CHAR)
) 
/
ALTER TABLE ORCHESTRATOR
ADD CONSTRAINT SYS_C0011933
PRIMARY KEY (ID)   
/
 
CREATE TABLE PLUGIN_META 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
DATE_CREATED TIMESTAMP (6), 
JSON_DATA CLOB, 
DATA_KEY VARCHAR2(255 CHAR), 
LAST_UPDATED TIMESTAMP (6), 
PROJECT VARCHAR2(255 CHAR)
) 
/
ALTER TABLE PLUGIN_META
ADD CONSTRAINT SYS_C0011727
PRIMARY KEY (ID)   
/
 
CREATE TABLE PROJECT 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
DATE_CREATED TIMESTAMP (6), 
DESCRIPTION VARCHAR2(255 CHAR), 
LAST_UPDATED TIMESTAMP (6), 
NAME VARCHAR2(255 CHAR)
)
/
 
CREATE TABLE RDOPTION 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
DEFAULT_VALUE CLOB, 
DELIMITER VARCHAR2(255 CHAR), 
DESCRIPTION CLOB, 
ENFORCED NUMBER(1,0), 
MULTIVALUED NUMBER(1,0), 
NAME VARCHAR2(255 CHAR), 
REGEX CLOB, 
REQUIRED NUMBER(1,0), 
SCHEDULED_EXECUTION_ID NUMBER(19,0), 
SECURE_EXPOSED NUMBER(1,0), 
SECURE_INPUT NUMBER(1,0), 
SORT_INDEX NUMBER(10,0), 
VALUES_URL VARCHAR2(255 CHAR), 
VALUES_URL_BLOB VARCHAR2(3000 CHAR), 
DEFAULT_STORAGE_PATH VARCHAR2(255 CHAR), 
VALUES_URL_CLOB VARCHAR2(3000 CHAR), 
CONFIG_DATA CLOB, 
DATE_FORMAT VARCHAR2(30 CHAR), 
IS_DATE NUMBER(1,0), 
MULTIVALUE_ALL_SELECTED NUMBER(1,0), 
OPTION_TYPE CLOB
) 
/
ALTER TABLE RDOPTION
ADD CONSTRAINT SYS_C0011794
PRIMARY KEY (ID)
/
 
CREATE TABLE RDOPTION_VALUES 
( OPTION_ID NUMBER(19,0), 
VALUES_STRING CLOB
)
/
 
CREATE TABLE RDUSER 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
DASHBOARD_PREF VARCHAR2(255 CHAR), 
DATE_CREATED TIMESTAMP (6), 
EMAIL VARCHAR2(255 CHAR), 
FILTER_PREF VARCHAR2(255 CHAR), 
FIRST_NAME VARCHAR2(255 CHAR), 
LAST_NAME VARCHAR2(255 CHAR), 
LAST_UPDATED TIMESTAMP (6), 
LOGIN VARCHAR2(255 CHAR), 
PASSWORD VARCHAR2(255 CHAR)
) 
/
ALTER TABLE RDUSER
ADD CONSTRAINT SYS_C0011739
PRIMARY KEY (ID)
/
 
CREATE TABLE REPORT_FILTER 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
CMD_FILTER VARCHAR2(255 CHAR), 
DOENDAFTER_FILTER NUMBER(1,0), 
DOENDBEFORE_FILTER NUMBER(1,0), 
DOSTARTAFTER_FILTER NUMBER(1,0), 
DOSTARTBEFORE_FILTER NUMBER(1,0), 
ENDAFTER_FILTER TIMESTAMP (6), 
ENDBEFORE_FILTER TIMESTAMP (6), 
JOB_FILTER VARCHAR2(255 CHAR), 
JOB_ID_FILTER VARCHAR2(255 CHAR), 
MAPREF_URI_FILTER VARCHAR2(255 CHAR), 
MESSAGE_FILTER VARCHAR2(255 CHAR), 
NAME VARCHAR2(255 CHAR), 
NODE_FILTER VARCHAR2(255 CHAR), 
OBJ_FILTER VARCHAR2(255 CHAR), 
PROJ_FILTER VARCHAR2(255 CHAR), 
RECENT_FILTER VARCHAR2(255 CHAR), 
REPORT_ID_FILTER VARCHAR2(255 CHAR), 
STARTAFTER_FILTER TIMESTAMP (6), 
STARTBEFORE_FILTER TIMESTAMP (6), 
STAT_FILTER VARCHAR2(255 CHAR), 
TAGS_FILTER VARCHAR2(255 CHAR), 
TITLE_FILTER VARCHAR2(255 CHAR), 
TYPE_FILTER VARCHAR2(255 CHAR), 
USER_ID NUMBER(19,0), 
USER_FILTER VARCHAR2(255 CHAR), 
EXECNODE_FILTER VARCHAR2(255 CHAR)
) 
/
ALTER TABLE REPORT_FILTER
ADD CONSTRAINT SYS_C0011749
PRIMARY KEY (ID)
/
ALTER TABLE REPORT_FILTER
ADD CONSTRAINT UK_9DH5OWJLBB2X223GOPAQ6T0JM
UNIQUE (NAME)   
/
 
CREATE TABLE SCHEDULED_EXECUTION 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
ARG_STRING CLOB, 
DATE_CREATED TIMESTAMP (6), 
DAY_OF_MONTH VARCHAR2(255 CHAR), 
DAY_OF_WEEK VARCHAR2(255 CHAR), 
DESCRIPTION CLOB, 
DO_NODEDISPATCH NUMBER(1,0), 
EXEC_COUNT NUMBER(19,0), 
FILTER CLOB, 
GROUP_PATH VARCHAR2(2048 CHAR), 
HOUR VARCHAR2(255 CHAR), 
JOB_NAME VARCHAR2(1024 CHAR), 
LAST_UPDATED TIMESTAMP (6), 
LOGLEVEL VARCHAR2(255 CHAR), 
MINUTE VARCHAR2(255 CHAR), 
MONTH VARCHAR2(255 CHAR), 
MULTIPLE_EXECUTIONS NUMBER(1,0), 
NEXT_EXECUTION TIMESTAMP (6), 
NODE_EXCLUDE CLOB, 
NODE_EXCLUDE_NAME CLOB, 
NODE_EXCLUDE_OS_ARCH CLOB, 
NODE_EXCLUDE_OS_FAMILY CLOB, 
NODE_EXCLUDE_OS_NAME CLOB, 
NODE_EXCLUDE_OS_VERSION CLOB, 
NODE_EXCLUDE_PRECEDENCE NUMBER(1,0), 
NODE_EXCLUDE_TAGS CLOB, 
NODE_INCLUDE CLOB, 
NODE_INCLUDE_NAME CLOB, 
NODE_INCLUDE_OS_ARCH CLOB, 
NODE_INCLUDE_OS_FAMILY CLOB, 
NODE_INCLUDE_OS_NAME CLOB, 
NODE_INCLUDE_OS_VERSION CLOB, 
NODE_INCLUDE_TAGS CLOB, 
NODE_KEEPGOING NUMBER(1,0), 
NODE_RANK_ATTRIBUTE VARCHAR2(255 CHAR), 
NODE_RANK_ORDER_ASCENDING NUMBER(1,0), 
NODE_THREADCOUNT NUMBER(10,0), 
PROJECT VARCHAR2(255 CHAR), 
RETRY CLOB, 
SCHEDULED NUMBER(1,0), 
SECONDS VARCHAR2(255 CHAR), 
SERVER_NODEUUID VARCHAR2(36 CHAR), 
TIMEOUT CLOB, 
TOTAL_TIME NUMBER(19,0), 
RDUSER VARCHAR2(255 CHAR), 
USER_ROLE_LIST CLOB, 
UUID VARCHAR2(255 CHAR), 
WORKFLOW_ID NUMBER(19,0), 
YEAR VARCHAR2(255 CHAR), 
EXECUTION_ENABLED NUMBER(1,0), 
LOG_OUTPUT_THRESHOLD VARCHAR2(256 CHAR), 
LOG_OUTPUT_THRESHOLD_ACTION VARCHAR2(256 CHAR), 
LOG_OUTPUT_THRESHOLD_STATUS VARCHAR2(256 CHAR), 
NODES_SELECTED_BY_DEFAULT NUMBER(1,0), 
ORCHESTRATOR_ID NUMBER(19,0), 
SCHEDULE_ENABLED NUMBER(1,0), 
NODE_FILTER_EDITABLE NUMBER(1,0)
) 
/
ALTER TABLE SCHEDULED_EXECUTION
ADD CONSTRAINT SYS_C0011802
PRIMARY KEY (ID)   
/
 
CREATE TABLE SCHEDULED_EXECUTION_FILTER 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
DESC_FILTER VARCHAR2(255 CHAR), 
GROUP_PATH VARCHAR2(255 CHAR), 
IDLIST VARCHAR2(255 CHAR), 
JOB_FILTER VARCHAR2(255 CHAR), 
LOGLEVEL_FILTER VARCHAR2(255 CHAR), 
NAME VARCHAR2(255 CHAR), 
PROJ_FILTER VARCHAR2(255 CHAR), 
USER_ID NUMBER(19,0)
) 
/
ALTER TABLE SCHEDULED_EXECUTION_FILTER
ADD CONSTRAINT SYS_C0011754
PRIMARY KEY (ID)
/
 
CREATE TABLE STORAGE 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
DATA BLOB, 
DATE_CREATED TIMESTAMP (6), 
DIR VARCHAR2(2048 CHAR), 
JSON_DATA CLOB, 
LAST_UPDATED TIMESTAMP (6), 
NAME VARCHAR2(1024 CHAR), 
NAMESPACE VARCHAR2(255 CHAR), 
PATH_SHA VARCHAR2(40 CHAR)
) 
/
ALTER TABLE STORAGE
ADD CONSTRAINT SYS_C0011810
PRIMARY KEY (ID)   
/
ALTER TABLE STORAGE
ADD CONSTRAINT SYS_C0011811
UNIQUE (PATH_SHA)    
/
 
CREATE TABLE WORKFLOW 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
KEEPGOING NUMBER(1,0), 
STRATEGY VARCHAR2(10 CHAR), 
THREADCOUNT NUMBER(10,0), 
PLUGIN_CONFIG CLOB
)
/
ALTER TABLE WORKFLOW
ADD CONSTRAINT SYS_C0011760
PRIMARY KEY (ID)
/
 
CREATE TABLE WORKFLOW_STEP 
( 
ID NUMBER(19,0), 
VERSION NUMBER(19,0), 
DESCRIPTION VARCHAR2(1024 CHAR), 
ERROR_HANDLER_ID NUMBER(19,0), 
KEEPGOING_ON_SUCCESS NUMBER(1,0), 
CLASS VARCHAR2(255 CHAR), 
JSON_DATA CLOB, 
NODE_STEP NUMBER(1,0), 
TYPE VARCHAR2(255 CHAR), 
ADHOC_EXECUTION NUMBER(1,0), 
ADHOC_FILEPATH CLOB, 
ADHOC_LOCAL_STRING CLOB, 
ADHOC_REMOTE_STRING CLOB, 
ARG_STRING CLOB, 
FILE_EXTENSION CLOB, 
INTERPRETER_ARGS_QUOTED NUMBER(1,0), 
SCRIPT_INTERPRETER CLOB, 
JOB_GROUP VARCHAR2(2048 CHAR), 
JOB_NAME VARCHAR2(1024 CHAR), 
NODE_FILTER CLOB, 
NODE_KEEPGOING NUMBER(1,0), 
NODE_RANK_ATTRIBUTE CLOB, 
NODE_RANK_ORDER_ASCENDING NUMBER(1,0), 
NODE_THREADCOUNT NUMBER(10,0), 
NODE_INTERSECT NUMBER(1,0)
) 
/
ALTER TABLE WORKFLOW_STEP
ADD CONSTRAINT SYS_C0011815
PRIMARY KEY (ID)    
/
 
CREATE TABLE WORKFLOW_WORKFLOW_STEP 
( 
WORKFLOW_COMMANDS_ID NUMBER(19,0), 
WORKFLOW_STEP_ID NUMBER(19,0), 
COMMANDS_IDX NUMBER(10,0)
) 
/
 
ALTER TABLE AUTH_TOKEN
ADD CONSTRAINT FK_AIQC20KPJASTH5BXOGSRAGOIF
FOREIGN KEY (USER_ID)
REFERENCES RDUSER (ID)
ENABLE
/
ALTER TABLE LOG_FILE_STORAGE_REQUEST
ADD CONSTRAINT FK_TRQSA9SO0QCV6OKCD6FAN88YF
FOREIGN KEY (EXECUTION_ID)
REFERENCES EXECUTION (ID)
ENABLE
/
ALTER TABLE EXECUTION
ADD CONSTRAINT FK_LTIUKR4BDTI8HCLO49O2G6V9O
FOREIGN KEY (ORCHESTRATOR_ID)
REFERENCES ORCHESTRATOR (ID)
ENABLE
/
ALTER TABLE JOB_FILE_RECORD
ADD CONSTRAINT FK_DGIG9THTEQ37SIDKC38MSXEP
FOREIGN KEY (EXECUTION_ID)
REFERENCES EXECUTION (ID)
ENABLE
/
 
ALTER TABLE EXECUTION
ADD CONSTRAINT FK_FOFKK7VJ9H2BDCQQICS5USTR6
FOREIGN KEY (WORKFLOW_ID)
REFERENCES WORKFLOW (ID)
ENABLE
/
ALTER TABLE EXECUTION
ADD CONSTRAINT FK_A908HRCN9U20EAYG6AKKEPBL1
FOREIGN KEY (RETRY_EXECUTION_ID)
REFERENCES EXECUTION (ID)
ENABLE
/
ALTER TABLE EXECUTION
ADD CONSTRAINT FK_6SHMC1Y7SH51X03AOVFUALOKA
FOREIGN KEY (SCHEDULED_EXECUTION_ID)
REFERENCES SCHEDULED_EXECUTION (ID)
ENABLE
/
ALTER TABLE NODE_FILTER
ADD CONSTRAINT FK_B7Y0UH7DSIIKF3GXN8RXVP1AJ
FOREIGN KEY (USER_ID)
REFERENCES RDUSER (ID)
ENABLE
/
ALTER TABLE NOTIFICATION
ADD CONSTRAINT FK_JNK8QDBQ6E3WWKCNI79WCREWY
FOREIGN KEY (SCHEDULED_EXECUTION_ID)
REFERENCES SCHEDULED_EXECUTION (ID)
ENABLE
/
ALTER TABLE SCHEDULED_EXECUTION
ADD CONSTRAINT FK_MNS7YXW0CX9MSUK59X9F2B4LV
FOREIGN KEY (ORCHESTRATOR_ID)
REFERENCES ORCHESTRATOR (ID)
ENABLE
/
ALTER TABLE RDOPTION_VALUES
ADD CONSTRAINT FK_P6S9N018KM1EMN0A0Q04YGC72
FOREIGN KEY (OPTION_ID)
REFERENCES RDOPTION (ID)
ENABLE
/
ALTER TABLE RDOPTION
ADD CONSTRAINT FK_JRAXRDPUL0NE9EEDIY1XJLSFV
FOREIGN KEY (SCHEDULED_EXECUTION_ID)
REFERENCES SCHEDULED_EXECUTION (ID)
ENABLE
/
ALTER TABLE REPORT_FILTER
ADD CONSTRAINT FK_BFOS9O64CND2I2XFNX7IL0TIX
FOREIGN KEY (USER_ID)
REFERENCES RDUSER (ID)
ENABLE
/
ALTER TABLE SCHEDULED_EXECUTION_FILTER
ADD CONSTRAINT FK_6R7OK07N10VBSBL6F1CK2H58U
FOREIGN KEY (USER_ID)
REFERENCES RDUSER (ID)
ENABLE
/
ALTER TABLE SCHEDULED_EXECUTION
ADD CONSTRAINT SYS_C0011803
UNIQUE (UUID)
/
ALTER TABLE SCHEDULED_EXECUTION
ADD CONSTRAINT FK_JLN7B7A683UWKL4FG6KPACQNP
FOREIGN KEY (WORKFLOW_ID)
REFERENCES WORKFLOW (ID)
ENABLE
/
ALTER TABLE WORKFLOW_STEP
ADD CONSTRAINT FK_8BBF05V4F6VO5O3CGP69AWCUE
FOREIGN KEY (ERROR_HANDLER_ID)
REFERENCES WORKFLOW_STEP (ID)
ENABLE
/
ALTER TABLE WORKFLOW_WORKFLOW_STEP
ADD CONSTRAINT FK_9PKEY6K5FDO6WORGQUAKKH7D1
FOREIGN KEY (WORKFLOW_STEP_ID)
REFERENCES WORKFLOW_STEP (ID)
ENABLE
/
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 
* To avoid a [Hibernate startup error](http://stackoverflow.com/questions/24050291/grails-hibernate4-upgrade-errors-on-
getgeneratedkeys), add the following command line when start rundeck:

```
-Dhibernate.jdbc.use_get_generated_keys=true
```

Edit the file `setenv.sh` in `$CATALINA_BASE/bin` or `/etc/tomcat/tomcat.conf` (Red Hat) to add the `hibernate.jdbc.use_get_generated_keys` parameter. For example:

```
JAVA_OPTS="$JAVA_OPTS -XX:MaxPermSize=256m -Xmx1024m -Xms256m -server -Drdeck.base=$RDECK_BASE -Drundeck.config.location=$RDECK_BASE/etc/rundeck-config.properties -Dhibernate.jdbc.use_get_generated_keys=true"
```