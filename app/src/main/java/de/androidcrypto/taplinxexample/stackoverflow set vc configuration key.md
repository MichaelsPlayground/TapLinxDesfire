Mifare DESFire EV3 set of VC Configuration key fails with Integrity Error (0x1E / 30 dec)

Tags: Android, Java, Mifare, Desfire

https://stackoverflow.com/questions/76412857/mifare-desfire-ev3-set-of-vc-configuration-key-fails-with-integrity-error-0x1e

I'm trying to set the VC (Virtual Card) Configuration key on a Mifare DESFire EV3 card. 

The VC Configuration key has the key number 0x20 (32 dec) as per AN12752 
MIFARE DESFire EV3 feature and functionality comparison to other MIFARE DESFire products:

<Screenshot>

I'm using NXP's TapLinx Android api in version 3.0 with this code (the card is detected by the api 
"type = libInstance.getCardType(intent);"):

            int timeOut = 2000;
            desFireEV3.getReader().setTimeout(timeOut);
            // select master application
            desFireEV3.selectApplication(0);
            // authenticate the master application with the PICC Master Key
            // DES default key
            byte[] TDES_KEY_ZERO = new byte[16]; // 16 bytes
            KeyData objKEY_TDES128ZERO = new KeyData();
            SecretKeySpec secretKeySpecTDesZero = new SecretKeySpec(TDES_KEY_ZERO, "TDES");
            objKEY_TDES128ZERO.setKey(secretKeySpecTDesZero);
            desFireEV3.authenticate(0, IDESFireEV3.AuthType.Native, KeyType.THREEDES, objKEY_TDES128ZERO);
            showMessage("authStatus 32: " + desFireEV3.getAuthStatus(), 'n');

            showMessage("change VC Configuration key (0x20)", 'n');
            try {
                byte NEW_KEY_VERSION = (byte) 0x00;
                desFireEV3.changeVCKey(32, new byte[16], new byte[16], NEW_KEY_VERSION);
                showMessage("change VC Configuration key (0x20) done", 'n');
            } catch (UsageException e) {
                showMessage("UsageException: " + e.getMessage(), 'n');
            } catch (InvalidResponseLengthException e) {
                showMessage("InvalidResponseLengthException: " + e.getMessage(), 'n');
            } catch (Exception e) {
                showMessage("Exception: " + e.getMessage(), 'n');
            }

Running this code gives these results in Logcat and ends with an **Integrity Error** (0x1E / 30 dec).:

NXPLog                  de.androidcrypto.taplinxexample      I  Set VC Configuration key (0x20)
NxpLogger               de.androidcrypto.taplinxexample      I  Command sent to card : 5A000000
NxpLogger               de.androidcrypto.taplinxexample      I  Response received : 00
NxpLogger               de.androidcrypto.taplinxexample      I  Command sent to card : 0A00
NxpLogger               de.androidcrypto.taplinxexample      I  Response received : AF0DCA56AAEA80FD9D
NxpLogger               de.androidcrypto.taplinxexample      I  Command sent to card : AF36E119DC9983B9E990AE554DA65151C6
NxpLogger               de.androidcrypto.taplinxexample      I  Response received : 00738F76587001AF44
SampleTapLinx           de.androidcrypto.taplinxexample      I  Dump Data: authStatus 32: Native
NXPLog                  de.androidcrypto.taplinxexample      I  authStatus 32: Native
SampleTapLinx           de.androidcrypto.taplinxexample      I  Dump Data: change VC Configuration key (0x20)
NXPLog                  de.androidcrypto.taplinxexample      I  change VC Configuration key (0x20)
SampleTapLinx           de.androidcrypto.taplinxexample      I  Dump Data: Exception: Integrity Error SW2 = 30
NXPLog                  de.androidcrypto.taplinxexample      I  Exception: Integrity Error SW2 = 30

What causes this error and how can I avoid it to change the key successfully ?
