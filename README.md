# BOTTLE-FILLING-PLANT
IT IS AN AUTOMATIC BOTTLE FILLING SIMULATION USING (WONDERWARE INTOUCH SCADA). THE SYSTEM AUTOMATES A CONTINUOUS BOTTLE TRANSPORT,SENSOR DETECTION, SORTING AND BOX PACKAGING USING INTOUCH WINDOW SCRIPTS.

ON SHOW CONDITION
S2 = 0;
S3 = 0;
L1 = 0;
L2 = 0;
L3 = 0;
LVL1 = 0;
P1 = 0;
T1 = 0;
B2 = 0;
M1 = 0;
V1 = 0;
BO2 = 0;
BOX_COUNT = 0;

WHILE SHOWING CONDITION
IF S3 == 1 THEN
    S2 = 0;
    M1 = 0;
    V1 = 0;
    B2 = 0;
    T1 = 100;
    P1 = 0;
    LVL1 = 0;
    L1 = 0;
    L2 = 0;
    L3 = 0;
    BO2 = 0;
    BOX_COUNT = 0;
ENDIF;

IF S2 == 1 AND S3 == 0 THEN
    L1 = 1;
    M1 = 1;
ENDIF;

{ --- 1. MOVING TO SENSOR --- }
IF M1 == 1 AND B2 < 220 AND S3 == 0 THEN
    B2 = B2 + 5;
    P1 = 0;
ENDIF;

{ --- 2. REACHED SENSOR -> STOP MOTOR & START FILLING --- }
IF M1 == 1 AND B2 >= 220 AND V1 == 0 AND T1 > 0 AND S3 == 0 THEN
    M1 = 0;
    P1 = 1;
    V1 = 1;
    L2 = 1;
    L1 = 0;
ENDIF;

{ --- 3. TANK EMPTYING PROCESS --- }
IF V1 == 1 AND S3 == 0 THEN
    IF T1 > 0 THEN
        T1 = T1 - 5;
    ENDIF;
ENDIF;

{ --- 4. FILLING COMPLETE -> CLOSE VALVE & RESTART MOTOR --- }
IF V1 == 1 AND T1 <= 0 AND S3 == 0 THEN
    LVL1 = 1;
    V1 = 0;
    L2 = 0;
    L3 = 1;
    L1 = 1;
    M1 = 1;
    P1 = 0;
    B2 = B2 + 5;
ENDIF;

{ --- 5. MOVING TO END AFTER FILLING --- }
IF M1 == 1 AND B2 > 220 AND B2 < 830 AND S3 == 0 THEN
    B2 = B2 + 5;
ENDIF;

{ --- 6. REACHED ROBOT AT 830 -> PICK, COUNT & BOX RESET --- }
IF B2 >= 830 AND S3 == 0 THEN
    M1 = 0;
    
    IF BO2 < 5 THEN
        BO2 = BO2 + 1;
    ENDIF;

    IF BO2 >= 5 THEN
        BOX_COUNT = BOX_COUNT + 1; 
        BO2 = 0;                    
    ENDIF;

    B2 = 0;
    T1 = 100;
    P1 = 0;
    LVL1 = 0;
    L3 = 0;
    L1 = 1;
    M1 = 1;
ENDIF;
