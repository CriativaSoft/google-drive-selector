# google-drive-selector
Allow use The Google Picker API from Any Domain

The communication  between domains is done using: `window.postMessage`  

Reference: https://www.gmass.co/blog/google-picker-api/


## Usage

Add to your page:

```javascript

var driveWindow;
var oauthToken = ""; // load from storage or keep black
var developerKey = 'XXXXX';
var clientId = "XXXXXX.apps.googleusercontent.com"
var appId = "XXXXXX";

function selectGoogleFile(){
    driveWindow = window.open('https://criativasoft.github.io/google-drive-selector', 'authWindow', "scrollbars=no,directories=no,titlebar=no,toolbar=no,location=no,status=no,menubar=no,titlebar=no,width="+screen.availWidth+",height="+screen.availHeight);    
}


// Function to be called from iframe
function onGoogleDriveSelected(message) {
    console.log(message);
    oauthToken = message['oauthToken'];
    driveWindow.close();
}

/* Chamado pelo 'driveWindow', avisando que foi carregado */
function onGoogleDriveLoaded(message) {

    var params = {
        'oauthToken' : oauthToken,
        'developerKey' : developerKey,
        'clientId' : clientId,
        'appId' : appId
    }


    // Obter o token e envia de forma segura.
    driveWindow.postMessage({
          'func': 'enablePicker',
          'message': params
      }, "*");
}


/**
 * Receive evensts from remote iframes/window and call local functions.
 */
function enableWindowCallbacks(){

    function _onMessage(event) {
        // Check sender origin to be trusted
        if (! event.origin.includes("github.io") && ! event.origin.includes("edu3.com.br")){
            console.error(event.origin + 'not permited !!');
            return;
        } 

        var data = event.data;

        if (typeof(window[data.func]) == "function") {
            window[data.func].call(null, data.message);
        }
    }
    
    if (window.addEventListener) {
        window.addEventListener("message", _onMessage, false);        
    } else if (window.attachEvent) {
        window.attachEvent("onmessage", _onMessage, false);
    }

}


enableWindowCallbacks();

```

Call

```html
<a href="#" onclick="selectGoogleFile()">Open</a>
```



