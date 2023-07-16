# Oracle APEX: Self-affirmations Mailing List app (in progress)
## DB
### MALING_LIST
  CREATE TABLE "MAILING_LIST" 
   (	"EMAIL" VARCHAR2(100) NOT NULL ENABLE, 
	"FIRST_NAME" VARCHAR2(50) NOT NULL ENABLE, 
	"LAST_NAME" VARCHAR2(50), 
	"DOB" DATE, 
	"GENDER" CHAR(1) NOT NULL ENABLE, 
	"DAYS" NUMBER NOT NULL ENABLE, 
	"PASSWORD" VARCHAR2(100) NOT NULL ENABLE, 
	"LAST_SENT" DATE DEFAULT CURRENT_DATE NOT NULL ENABLE, 
	 CONSTRAINT "MAILING_LIST_PK" PRIMARY KEY ("EMAIL")
  USING INDEX  ENABLE
   ) ;

   COMMENT ON TABLE "MAILING_LIST"  IS 'The users who signed up for the Self Affimations Program';
### MAILING_LIST_TERMS
CREATE TABLE "MAILING_LIST_TERMS" 
   (	"EMAIL" VARCHAR2(100 CHAR) NOT NULL ENABLE, 
	"TYPE" VARCHAR2(100 CHAR) NOT NULL ENABLE, 
	"TERM" VARCHAR2(100 CHAR) NOT NULL ENABLE, 
	 CONSTRAINT "MAILING_LIST_INTERESTS_PK" PRIMARY KEY ("EMAIL", "TYPE", "TERM")
  USING INDEX  ENABLE
   ) ;

  ALTER TABLE "MAILING_LIST_TERMS" ADD FOREIGN KEY ("EMAIL")
	  REFERENCES "MAILING_LIST" ("EMAIL") ENABLE;
### API endpoints
- https://apex.oracle.com/pls/apex/teochewthunder/mailinglist/readytoreceive
    - GET
    - Collection Query
    - SELECT EMAIL, FIRST_NAME, LAST_NAME, DOB, GENDER FROM MAILING_LIST WHERE LAST_SENT < TO_DATE(CURRENT_DATE - DAYS)
- https://apex.oracle.com/pls/apex/teochewthunder/mailinglist/setreceived/:email
    - GET
    - PL/SQL
    - UPDATE MAILING_LIST SET LAST_SENT = CURRENT_DATE  WHERE EMAIL = :email
- https://apex.oracle.com/pls/apex/teochewthunder/mailinglist/terms/:email
    - GET
    - Collection Query
    - SELECT TYPE, TERM FROM MAILING_LIST_TERMS WHERE EMAIL = :email
  
## Pages (All pages are Public)
### Registration (P2)
#### Form
- Mailing List
    - Table: MAILING_LIST
- Processing
    - INSERT INTO MAILING_LIST ("EMAIL", "FIRST_NAME", "LAST_NAME", "DOB", "DAYS", "GENDER", "PASSWORD") VALUES (:P2_EMAIL, :P2_FIRST_NAME, :P2_LAST_NAME, :P2_DOB, :P2_DAYS, :P2_GENDER, DBMS_OBFUSCATION_TOOLKIT.MD5(INPUT => UTL_RAW.CAST_TO_RAW (:P2_PASSWORD)))
- After Processing
    - Redirect to P10
#### Fields
- P2_EMAIL
    - Type: Text Field
    - Subtype: Email
    - Validation: upper(:P2_EMAIL) not in (SELECT upper(EMAIL) FROM MAILING_LIST
- P2_FIRST_NAME
    - Type: Text Field
- P2_LAST_NAME
    - Type: Text Field
- P2_DOB
    - Type: Date Picker
    - Maximum Static: -15y 
- P2_GENDER
    - Type: Radio Group
    - Static values: M. F
    - Default: M 
- P2_INTERVAL
    - Type: Select List
    - Static values: Once per day (1), Every two days (2), Every week (7), Every two weeks (14), Every month (30)
- P2_PASSWORD
    - Type: Password
- P2_CONFIRMPASSWORD
    - Type: Password
    - Validation: :P2_CONFIRMPASSWORD = :P2_PASSWORD
#### Buttons
- Register
    - Action: Submit

### Registration Thankyou (P10)
#### Buttons
- Login
    - Action: Redirect to P1

### Login (P1) 
#### Form
- Mailing List
    - Table: MAILING_LIST
- After Processing
    - Redirect to P3
#### Fields
- EMAIL
    - Type: Text Field
    - Subtype: Email
    - Validation: :EMAIL in (SELECT EMAIL FROM MAILING_LIST WHERE upper(EMAIL) = upper(:EMAIL) AND PASSWORD = DBMS_OBFUSCATION_TOOLKIT.MD5(INPUT => UTL_RAW.CAST_TO_RAW (:PASSWORD)))
- PASSWORD
    - Type: Password
#### Buttons
- Login
    - Action: Submit
- Register
    - Action: Redirect to P2


### Update (P3)
#### Form
- Pre-rendering
    - IF (:EMAIL IS NULL) THEN apex_util.REDIRECT_URL(1); END IF;
    - SELECT FIRST_NAME, LAST_NAME, DOB, GENDER, DAYS INTO :P3_FIRST_NAME, :P3_LAST_NAME, :P3_DOB, :P3_GENDER, :P3_DAYS FROM MAILING_LIST WHERE EMAIL = :EMAIL;
- Mailing List
    - Table: MAILING_LIST
    - Processing
        - UPDATE MAILING_LIST SETFIRST_NAME = :P3_FIRST_NAME, LAST_NAME = :P3_LAST_NAME, DOB = :P3_DOB, GENDER = :P3_GENDER, DAYS = :P3_DAYS WHERE upper(EMAIL) = upper(:EMAIL)
#### Fields
- P3_FIRST_NAME
    - Type: Text Field
- P3_LAST_NAME
    - Type: Text Field
- P3_DOB
    - Type: Date Picker
    - Maximum Static: -15y 
- P3_GENDER
    - Type: Radio Group
    - Static values: M. F
    - Default: M 
- P3_INTERVAL
    - Type: Select List
    - Static values: Once per day (1), Every two days (2), Every week (7), Every two weeks (14), Every month (30)
#### Buttons
- Register
    - Action: Submit


- Interests
    - Interest Modal
- Descriptions
    - Description Modal
- Logout
- Shared Components
