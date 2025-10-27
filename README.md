# BugBountyPlaybook
 # A compact, battle-ready bug-bounty playbook

    1. Scope & Recon checklist
   
       Confirm program scope, policy, and exclusions (assets, endpoints, subdomains).

       Confirm disclosure timeline / bounty rules / safe harbor.
 
       Create a unique test-id (e.g., mbp-<YYYYMMDD>-<rand>) to tag callbacks.

       Enumerate assets: main domain, subdomains, IP ranges, CDN, third-party endpoints, S3 buckets, api endpoints.

2. Fast recon commands (run from a Kali/Ubuntu box)

  # Passive discovery
   
   # subdomains (multi-tool):
      subfinder -silent -d target.com -o subs.txt
      assetfinder --subs-only target.com >> subs.txt
      amass enum -passive -d target.com -o subs.txt

   # wayback + urls:
      cat subfinder.txt amass_passive.txt | sort -u > domains.txt
      for d in $(cat domains.txt); do
        echo "=== $d ==="
        waybackurls $d | anew wayback_urls.txt
        gau $d | anew gau_urls.txt
      done

  # Active discovery

   # port scan:
     masscan -p1-65535 --rate=1000 -oG masscan.gnmap target.com

   # nmap for interesting hosts:
    nmap -sC -sV -p- -T4 -oA nmap_full target.com

  # Content & endpoints
   # get endpoints from common tools:
    httpx -l wayback_urls.txt -silent -threads 50 -o httpx_alive.txt
    feroxbuster -u https://target.com -w /usr/share/wordlists/dirb/common.txt -o ferox.txt
   # Automate subdomain takeover checks: 
    subjack -w domains.txt -t 50 -timeout 30 -o subjack.txt -v

     
