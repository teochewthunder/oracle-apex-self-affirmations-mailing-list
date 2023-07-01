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
- https://apex.oracle.com/pls/apex/teochewthunder/mailinglist/setreceived/:email
- https://apex.oracle.com/pls/apex/teochewthunder/mailinglist/terms/:email
  
## Pages
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
- P2_ CONFIRMPASSWORD
    - Type: Password
    - Validation: :P2_CONFIRMPASSWORD = :P2_PASSWORD
#### Buttons
- Register
    - Action: Submit

### Registration Thankyou (P10)
#### Buttons
- Login
    - Action: Redirect to P1

- Login
- Update
- Interests
    - Interest Modal
- Descriptions
    - Description Modal
- Logout
