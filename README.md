# Voice Platform Script Demo
This repository contains information on how to attach a custom call flow Voice API script to a Voyant Phone Number.

# The Script
This script ([in this repository](https://github.com/inteliquent/vp-registration-script-demo/blob/master/script-example.xml)) accomplishes the following use-case:
- We receive the inbound Call (LegA) and:
  - Play ringing in the background
  - Then, we Dial out to the SIP Endpoint for legB
    - When the user picks up, it's either the voicemail, or a real person, so we ask them to press a Key to accept the call (using TTS)
      - If they press 1, then we join them to LegA so the two parties can talk
      - If they don’t press 1, then we terminate LegB and transition LegA to Voicemail:
        - Stop playing ringback on LegA
        - Play the voicemail instructions using TTS
        - Play a Beep
          - Turn on call recording to record a voicemail.
  - Once the call is over, or other significant events occur, we send those events via web hook to a backend for recording.
        

# Event Logs

The script will POST events to the following HTTP Request Logger using our `query` XML verb:

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

![Edit via Admin Portal](https://www.dropbox.com/s/cyrt2n6adahbzjt/Screenshot%202019-11-05%2010.59.27.png?dl=0&raw=true)

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

![Provisioning QR Code](https://www.dropbox.com/s/i39vopm1dmld67p/cloudsoftphone-qr-ADHOCLABS-1-e4aa4d23213daeee3612c07af8a009ad-live-660px.png?dl=0&raw=true)

The app will register via SIP and will ring if one of the test numbers (associated with the script in this repository) are called.

# Further Documentation

Documentation on assigning and unassigning scripts to phone numbers can be found here:

https://voyant.docs.apiary.io/#reference/voice-api/add-voice-api-service-to-a-number

Documentation on Voice API XML functionality can be found here:

https://voyantvoiceapi.docs.apiary.io/#introduction/overview


