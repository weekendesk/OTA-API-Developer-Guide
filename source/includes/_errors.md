# Errors

<aside class="notice">These are all errors you can receive while sending a request to Weekendesk system. Remember once the requests respects the format and pass the validation, all errors will be returned in a form of XML message and the status will be 200.</aside>

The OTA ARI API uses the following error codes:


Status Code | Meaning
---------- | -------
200 | Success -- Your request has been correctly parsed.
500 | Internal Server Error -- Our server cannot parse your request

Error Code | Meaning
---------- | -------
Code="A01.RIT" | Unauthorised Client -- The IP or the credentials used by the Channel partner are not valid.
Code="2.1" | ERROR: Hotel ID not found - The Hotel ID used in the request is not valid.
Code="2.1" | ERROR: Rate Plan Code not found -- The Room ID used in the request is not valid.
Code="2.1" | ERROR: Room ID not found -- The Rate Plan Code used in the request is not valid.
Code="2.1" | ERROR: The request is missing a mandatory item -- One of the parameter used in the request is missing.
Code="2.1" | Wrong value -- One of the values used in the request is not of the right format.
Code="2.1" | Amount must be a positive number grater than zero -- Wrong value used in the request.
Code="2.1" | Invalid date - Start date is before today -- The Start date used is in the past.
Code="2.1" | Invalid date - End date should not exceed 1 year -- The updated period should be less than 1 year.
Code="2.1" | Invalid date - Start date is after End date -- The Start date cannot be after the End date.
