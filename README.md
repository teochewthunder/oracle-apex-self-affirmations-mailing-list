# Oracle APEX Self-affirmations Mailing List app
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
	"LAST_SENT" DATE DEFAULT '01-01-1970' NOT NULL ENABLE, 
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
- REGISTER
    - Action: Submit

### Registration Thankyou (P10)
#### Buttons
- LOGIN
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
- LOGIN
    - Action: Submit
- REGISTER
    - Action: Redirect to P2


### Update (P3)
- Pre-rendering
    - IF (:EMAIL IS NULL) THEN apex_util.REDIRECT_URL(1); END IF;
    - SELECT FIRST_NAME, LAST_NAME, DOB, GENDER, DAYS INTO :P3_FIRST_NAME, :P3_LAST_NAME, :P3_DOB, :P3_GENDER, :P3_DAYS FROM MAILING_LIST WHERE EMAIL = :EMAIL;
- Top Navigation: (shared Comp)
#### Form
- Mailing List
    - Table: MAILING_LIST
    - Processing
        - UPDATE MAILING_LIST SET FIRST_NAME = :P3_FIRST_NAME, LAST_NAME = :P3_LAST_NAME, DOB = :P3_DOB, GENDER = :P3_GENDER, DAYS = :P3_DAYS WHERE upper(EMAIL) = upper(:EMAIL)
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
- OPENPASSWORDMODAL
    - Action: Redirect to P8
- UPDATE
    - Action: Submit

### Update Password (P8) (Modal)
- Pre-rendering
    - IF (:EMAIL IS NULL) THEN apex_util.REDIRECT_URL(1); END IF;
#### Form
- Mailing List
    - Table: MAILING_LIST
    - Processing
        - UPDATE MAILING_LIST SET password = DBMS_OBFUSCATION_TOOLKIT.MD5(INPUT => UTL_RAW.CAST_TO_RAW (:P8_PASSWORD)) WHERE upper(EMAIL) = upper(:EMAIL)
#### Fields
- P8_PASSWORD
    - Type: Password
- P8_CONFIRMPASSWORD
    - Type: Password
    - Validation: :P8_CONFIRMPASSWORD = :P8_PASSWORD
#### Buttons
- UPDATEPASSWORD
    - Action: Submit
 
### Logout (P9)
- Pre-rendering
    - Clear Current Session
#### Buttons
- BACKTOLOGIN
    - Action: Redirect to P1

### Interests (P4)
- Pre-rendering
    - IF (:EMAIL IS NULL) THEN apex_util.REDIRECT_URL(1); END IF;
- Top Navigation: (shared Comp)
#### ListView
- upper(EMAIL) = upper(:EMAIL) AND TYPE = 'INTERESTS'
- Text Column: TERM
- Link Target: 6
#### Form
- New Interest
    - Table: MAILING_LIST_TERMS
    - Processing: INSERT INTO MAILING_LIST_TERMS (EMAIL, TYPE, TERM) VALUES (:EMAIL, 'INTERESTS', :P4_TERM);
#### Fields
- P4_TERM
    - Type: Text Field
    - Validation: upper(:P4_TERM) not in (SELECT upper(TERM) FROM MAILING_LIST_TERMS WHERE EMAIL = :EMAIL AND TYPE = 'INTERESTS')
#### Buttons
- ADDNEWINTEREST
    - Action: Submit

### Delete Interest (P6) (Modal)
#### Form
- Delete Interest
    - Table: MAILING_LIST_TERMS
    - Processing: DELETE FROM MAILING_LIST_TERMS WHERE EMAIL = :EMAIL AND TYPE = 'INTERESTS' AND TERM = :P6_TERM;
- After Processing
    - Redirect to P4
#### Fields
- P4_TERM
    - Type: Text Field
    - ReadOnly
#### Buttons
- DELETEINTEREST
    - Action: Submit

    
### Descriptions (P5)
- Pre-rendering
    - IF (:EMAIL IS NULL) THEN apex_util.REDIRECT_URL(1); END IF;
- Top Navigation: (shared Comp)
#### ListView
- upper(EMAIL) = upper(:EMAIL) AND TYPE = 'DESCRIPTIONS'
- Text Column: TERM
- Link Target: 7
#### Form
- New Description
    - Table: MAILING_LIST_TERMS
    - Processing: INSERT INTO MAILING_LIST_TERMS (EMAIL, TYPE, TERM) VALUES (:EMAIL, 'DESCRIPTIONS', :P5_TERM);
#### Fields
- P5_TERM
    - Type: Text Field
    - Validation: upper(:P5_TERM) not in (SELECT upper(TERM) FROM MAILING_LIST_TERMS WHERE EMAIL = :EMAIL AND TYPE = 'DESCRIPTIONS')
#### Buttons
- ADDNEWDESCRIPTION
    - Action: Submit

### Delete Description (P7) (Modal)
#### Form
- Delete Description
    - Table: MAILING_LIST_TERMS
    - Processing: DELETE FROM MAILING_LIST_TERMS WHERE EMAIL = :EMAIL AND TYPE = 'DESCRIPTIONS' AND TERM = :P7_TERM;
- After Processing
    - Redirect to P4
#### Fields
- P7_TERM
    - Type: Text Field
    - ReadOnly
#### Buttons
- DELETEDESCRIPTION
    - Action: Submit


- Shared Components
