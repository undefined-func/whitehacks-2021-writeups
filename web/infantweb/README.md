# infantweb
### TEAM NAME: NYCP

## CATEGORY
Web

## DESCRIPTION


## FLAG
`WH2021{Youre_on_track_for_w3b_greatNess!}``

## WRITEUP
For the first challenge `Basics of Web Requests`, we simply view page source or inspect element to get to the next stage. The URL is hidden in the HTML code as a comment.

```html
        <!--
        TODO: John, once you've completed the web recon page
        at /web-recon-secret-url-31337.html please help
        me to update the href in this element. Thanks :)
        -->
```

For the second challenge, we have to find a common file. We can Google for common files on web server or ~~dirbuster the challenge site~~.

From this [Wikipedia page](https://en.wikipedia.org/wiki/List_of_/.well-known/_services_offered_by_webservers)  of well-known services, we find our answer: `security.txt`.

Going to `/security.txt` returns us the following document:
```
Contact: mailto:goodjob@idiot.sg
Acknowledgments: https://blog.idiot.sg/
Preferred-Languages: en
Policy: http://whateverwebsiteweuse.com/final-section-lets-get-flag-8008.html
```

For the last challenge, we have to understand the following Javascript:
```js
//   __                        _                   
//  / _| ___   ___ _   _ ___  | |__   ___ _ __ ___
// | |_ / _ \ / __| | | / __| | '_ \ / _ \ '__/ _ \
// |  _| (_) | (__| |_| \__ \ | | | |  __/ | |  __/
// |_|  \___/ \___|\__,_|___/ |_| |_|\___|_|  \___|
//
//                      ||
//                      \/
//
// This function is called each time you press the "Submit"
// button, verifying whether you typed the correct password
function checkPassword(){
    // password contains the password the user typed
    var password = document.getElementById("passwd").value;

    if(password[0] == 'i'){
        if(password[1] == 'd'){
            if(password[2] == 'i'){
                if(password[3] == 'o'){
                    if(password[4] == 't'){
                        flag(password);
                        return;
                    }
                }
            }
        }
    }
```

The script basically takes in the input and does some string slicing to compare each character. The password is `idiot`.

We then get the flag: `WH2021{Youre_on_track_for_w3b_greatNess!}`
