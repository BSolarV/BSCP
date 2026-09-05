1. In Burp's browser, access the lab and log in to the application using the credentials `wiener:peter`.
    
2. In Burp, go to **Proxy > HTTP history**. Right-click the `GET /user/lookup?user=wiener` request and select **Send to Repeater**.
    
3. In Repeater, submit a `'` character in the user parameter. Notice that this causes an error. This may indicate that the user input was not filtered or sanitized correctly.
    
4. Submit a valid JavaScript payload in the `user` parameter. For example, you could use `wiener'+'`
    
    Make sure to URL-encode the payload by highlighting it and using the hotkey `Ctrl-U`. Notice that it retrieves the account details for the `wiener` user, which indicates that a form of server-side injection may be occurring.
    
5. Identify whether you can inject boolean conditions to change the response:
    
    1. Submit a false condition in the `user` parameter. For example: `wiener' && '1'=='2`
        
        Make sure to URL-encode the payload. Notice that it retrieves the message `Could not find user`.
        
    2. Submit a true condition in the user parameter. For example: `wiener' && '1'=='1`
        
        Make sure to URL-encode the payload. Notice that it no longer causes an error. Instead, it retrieves the account details for the `wiener` user. This demonstrates that you can trigger different responses for true and false conditions.
        
6. Identify the password length:
    
    1. Change the user parameter to `administrator' && this.password.length < 30 || 'a'=='b`, then send the request.
        
        Make sure to URL-encode the payload. Notice that the response retrieves the account details for the `administrator` user. This indicates that the condition is true because the password is less than 30 characters.
        
    2. Reduce the password length in the payload, then resend the request.
    3. Continue to try different lengths.
    4. Notice that when you submit the value `9`, you retrieve the account details for the `administrator` user, but when you submit the value `8`, you receive an error message because the condition is false. This indicates that the password is 8 characters long.
7. Right-click the request and select **Send to Intruder**.
    
8. In Intruder, enumerate the password:
    
    1. Change the user parameter to `administrator' && this.password[§0§]=='§a§`. This includes two payload positions. Make sure to URL-encode the payload.
    2. Select **Cluster bomb attack** from the attack type drop-down menu.
    3. In the **Payloads** side panel, select position `1` from the **Payload position** drop-down list. Add numbers from 0 to 7 for each character of the password.
    4. Select position `2` from the **Payload position** drop-down list, then add lowercase letters from a to z. If you're using Burp Suite Professional, you can use the built-in `a-z` list.
    5. Click  **Start attack**.
    6. Sort the attack results by **Payload 1**, then **Length**. Notice that one request for each character position (0 to 7) has evaluated to true and retrieved the details for the `administrator` user. Note the letters from the **Payload 2** column down.
9. In Burp's browser, log in as the `administrator` user using the enumerated password. The lab is solved.