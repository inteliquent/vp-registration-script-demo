# Voice Platform Script Demo
This repository contains information on how to attach a custom call flow Voice API script to a Voyant Phone Number.

# The Script
This script (in this repository) accomplishes the following use-case:
- We receive the inbound Call (LegA) and:
  - Play ringing in the background
  - Then, we Dial out to the SIP Endpoint for legB
    - When the user picks up, its either the voicemail, or a real person, so we ask them to press a Key to accept the call (using TTS)
      - If they press 1, then we join them to the Conference Room
      - If they don’t press 1, then we terminate LegB and stop playing ringback, play the voicemail instructions using TTS, play a Beep, and turn on call recording to record a voicemail.
  - Once the call is over, or other significant events occur, we send those events via web hook to a backend for recording.
        

# Event Logs

The script will POST events to the following HTTP Request Logger:

http://webhook.site/#!/3324d98c-21ec-4fc2-9d9f-84c38f57b77a/0e015977-8a7a-4b1d-90b2-194060b87e61/1


# Assigning the Script to a Phone Number
POST https://api.voyant.com/voice/api/{{PHONE_NUMBER}}/script?sessionId={{API_TOKEN}}

BODY: {{SCRIPT XML CONTENT}}

## CURL Example

```
curl -X POST \
  'https://api.voyant.com/voice/api/13182495699/script?sessionId={{API_TOKEN}}' \
  -H 'Accept: */*' \
  -H 'Accept-Encoding: gzip, deflate' \
  -H 'Cache-Control: no-cache' \
  -H 'Connection: keep-alive' \
  -H 'Content-Length: 7204' \
  -H 'Content-Type: application/xml' \
  -H 'Host: api.voyant.com' \
  -d '<script>
    <head></head>
    <body>        
        <!-- Accept the inbound call -->
        <accept></accept>

        <!-- Send back 180 ringing -->
        <ring></ring>

        <!-- Answer the call -->
        <answer alias="{{X-OrigCallId}}">
            <!-- When the call connects, execute the "dial_user" snippet, defined above -->
            <onConnect>
                <say TEXT="Hello World"></say>
            </onConnect>
        </answer>
    </body>
</script>'
```

# Test Phone Numbers

The XML script in this repository is currently associated with 2 phone numbers:
- 1 (512) 488-0395
- 1 (318) 249-5699

The script associated with these phone numbers can be edited in the Voyant Admin Portal or via our REST API.

**Voyant Admin Portal Example**

![Edit via Admin Portal](https://previews.dropbox.com/p/thumb/AAmRGY_GR47iBX4325j-gS_jeV4XB0Qz4b0_Iuy4d-TVnZPxzXWwXnL0XjoC6YxEPiVFved8mRHEs5CsMzelyZdjKMADSr14_Zi-CeqDpWODhK_efMQBFdaXDp-MemRecknM6ygGMrc7AKmbFFhIcjJmLsyFYW9gS1B7dqWRd2AlFPOm8mkqhmP79M4DmaMaTCUjKheVtZKSy2Udcrq1Phts7oH5f6VRDcrJPZfrdnDn8OAHECcg_oP1B4sgdK67kErpH0FXO72DBlr5Iow1dKPVtbXqr5AHXjA5TIJW5Ignjg8VQycg-9jAWlsKjV7UgGBG4YfG1K7wV7biRPwphNImhRAR0SC3vODlSc4R3DF72IWijtARGvTS-92xm1RwX9zvfru5mHpQZyrku9VDrb4YdELhs4mm2bb9TCutovW8Vw/p.png?size=2048x1536&size_mode=3)

**REST API Example**

GET https://api.voyant.com/voice/api/{{PHONE_NUMBER}}/script?sessionId={{API_TOKEN}}

## CURL Example

```
curl -X GET \
  'https://api.voyant.com/voice/api/13182495699/script?sessionId={{API_TOKEN}}' \
  -H 'Accept: */*' \
  -H 'Accept-Encoding: gzip, deflate' \
  -H 'Cache-Control: no-cache' \
  -H 'Connection: keep-alive' \
  -H 'Content-Type: application/xml' \
  -H 'Host: api.voyant.com' \
  -H 'User-Agent: PostmanRuntime/7.18.0' \
```

# Testing with a SIP Client

The script is configured to dial out to a registered SIP User Agent. To test this functionality, download the "Cloud SoftPhone" App in the Apple or Google App Stores.

After opening the app for the first time, scan this QR code to provision the application for this demo.

![Provisioning QR Code](https://previews.dropbox.com/p/thumb/AAnRROADk4PUsrf-FcDiqEQV5gx51diQ4bp6ubRecCEnb16-ih0eu7uEmDsvNXecBytXfFRdxVA6HY4PX_fd0zP_YFhi95a1dKz5uo2Kc-JFCKda_GHLs7iAQ07tF5B_TVqlSU4uuopHyavhGO1eNRnhqU8cb3-CSlaCRVGcKje8iG9xfstiFyOOs5MUpuQv63Put8n0vdCH0bNujhCD7tGPaBEL2-HVlluSTXbVIim5pzzr1N-sVQBizey7Hwt4iKbCjXAImUX5yXWVFOoaqX6dI5eR5uPA3V37QsjtXkZyqvyyuBhsDMUn5HnlY50lZxW0EeDcBd69Ivcs5u1PEmpZ02LZotFmknYRqAuDRWOJtHXke1edEnklS1E4AAJtiLYy6tpTrAhXMfE1cRqYM1cy2pD8vvFOTsvjqJwVJnlKIPRGXqgZCqe-rLAgsrfst3keJBK6epf5GO1Uc5sb3bli8FiDslt8CVjhuOPsqihZaKB7K2lXH6UyqEFtAZmTA1RvV6tmVkmEMETfs0MyL4Dp/p.png?fv_content=true&size_mode=5)

The app will register via SIP and will ring if one of the test numbers (associated with the script in this repository) are called.



