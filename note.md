> split column in google sheet

=IF(C2<>"", LEFT(C2, FIND(",", C2) - 1), "")

> google sheet script for adding fields in google

var sheetName = 'Sheet1'
var scriptProp = PropertiesService.getScriptProperties()

function intialSetup () {
var activeSpreadsheet = SpreadsheetApp.getActiveSpreadsheet()
scriptProp.setProperty('key', activeSpreadsheet.getId())
}

function doPost (e) {
var lock = LockService.getScriptLock()
lock.tryLock(10000)

try {
var doc = SpreadsheetApp.openById(scriptProp.getProperty('key'))
var sheet = doc.getSheetByName(sheetName)

    var headers = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0]
    var nextRow = sheet.getLastRow() + 1

    var newRow = headers.map(function(header) {
      return header === 'timestamp' ? new Date() : e.parameter[header]
    })

    sheet.getRange(nextRow, 1, 1, newRow.length).setValues([newRow])

    return ContentService
      .createTextOutput(JSON.stringify({ 'result': 'success', 'row': nextRow }))
      .setMimeType(ContentService.MimeType.JSON)

}

catch (e) {
return ContentService
.createTextOutput(JSON.stringify({ 'result': 'error', 'error': e }))
.setMimeType(ContentService.MimeType.JSON)
}

finally {
lock.releaseLock()
}
}

// location fetcing
function doGet(e) {
return HtmlService.createHtmlOutputFromFile('index');
}

function saveCoordinates(latitude,longitude, locationpin){
var latlongcol, pinlocationcol;
const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("sheet1");
var lastrow= sheet.getLastRow();
var header = sheet.getRange(1, 1, 1,sheet.getLastColumn()).getValues();
header=header.flat();
for(var i=1;i<=header.length;i++){

    if(header[i]=="LatLong")
    {
      latlongcol=i+1;
    }

    if(header[i]=="GeoLocation"){

      pinlocationcol=i+1;
    }

}
if(sheet.getRange(lastrow, 1).getValue()!="" && sheet.getRange(lastrow, latlongcol).getValue()=="" && sheet.getRange(lastrow, pinlocationcol).getValue()==""){
sheet.getRange(lastrow, latlongcol).setValue(latitude +" , "+ longitude);
sheet.getRange(lastrow, pinlocationcol).setValue(locationpin);
}
}

> Fetching location lat, long with button in html

<!-- Location fetching -->

      <label for="latitude">Latitude:</label>

<input type="text" id="latitude" name="latitude">

<label for="longitude">Longitude:</label>
<input type="text" id="longitude" name="longitude">

<button type="button" onclick="fetchLocation()">Get Location</button>

<script>
    function fetchLocation() {
        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(successCallback, errorCallback);
        } else {
            alert("Geolocation is not supported by your browser.");
        }
    }

    function successCallback(position) {
        var latitude = position.coords.latitude;
        var longitude = position.coords.longitude;

        document.getElementById('latitude').value = latitude;
        document.getElementById('longitude').value = longitude;
    }

    function errorCallback(error) {
        switch (error.code) {
            case error.PERMISSION_DENIED:
                alert("Location permission denied. Please enable location services.");
                break;
            case error.POSITION_UNAVAILABLE:
                alert("Location information is unavailable.");
                break;
            case error.TIMEOUT:
                alert("The request to get user location timed out.");
                break;
            case error.UNKNOWN_ERROR:
                alert("An unknown error occurred while getting the location.");
                break;
        }
    }
</script>

<!-- End of Location fetching -->

<!-- google sheet script -->

var sheetName = 'salesSampling';
var scriptProp = PropertiesService.getScriptProperties();
var recipientEmail = 'muzaffarrafiq1717@gmail.com'; // Specify the email address to which the notification should be sent

function intialSetupSampling() {
var activeSpreadsheet = SpreadsheetApp.getActiveSpreadsheet();
scriptProp.setProperty('key', activeSpreadsheet.getId());
}

function doPost(e) {
var lock = LockService.getScriptLock();
lock.tryLock(10000);

try {
var doc = SpreadsheetApp.openById(scriptProp.getProperty('key'));
var sheet = doc.getSheetByName(sheetName);

    var headers = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0];
    var nextRow = sheet.getLastRow() + 1;

    var newRow = headers.map(function(header) {
      return header === 'timestamp' ? new Date() : e.parameter[header];
    });

    sheet.getRange(nextRow, 1, 1, newRow.length).setValues([newRow]);

    // Send email notification
    var subject = 'New row added in Google Sheets (Sales Sampling)';
    var message = 'A new row has been added in the sheet: ' + sheetName + '\n\n';
    message += 'Row: ' + nextRow + '\n';
    message += 'Column values: ' + newRow.join(', ');

    MailApp.sendEmail(recipientEmail, subject, message);

    return ContentService
      .createTextOutput(JSON.stringify({ 'result': 'success', 'row': nextRow }))
      .setMimeType(ContentService.MimeType.JSON);

} catch (e) {
return ContentService
.createTextOutput(JSON.stringify({ 'result': 'error', 'error': e }))
.setMimeType(ContentService.MimeType.JSON);
} finally {
lock.releaseLock();
}
}

<!-- end of google sheet script -->
