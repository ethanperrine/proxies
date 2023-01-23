from typing import Union, List
import re, json
from concurrent.futures import ThreadPoolExecutor
from concurrent.futures import as_completed

try:
    import requests
except ImportError:
    print("pip install requests")
    exit()
    

list_of_proxy_sources = [
    "https://raw.githubusercontent.com/mertguvencli/http-proxy-list/main/proxy-list/data.txt",
    "https://raw.githubusercontent.com/TheSpeedX/PROXY-List/master/http.txt",
    "https://github.com/ShiftyTR/Proxy-List/blob/master/http.txt",
    "https://raw.githubusercontent.com/ShiftyTR/Proxy-List/master/https.txt",
    "https://api.proxyscrape.com/v2/?request=displayproxies&protocol=http&timeout=10000&country=all&ssl=all&anonymity=all",
    "https://raw.githubusercontent.com/jetkai/proxy-list/main/online-proxies/txt/proxies.txt",
    ]

'''
Github Definitions:
'''

def upload_to_github(list: List[str]) -> None:
    OWNER = "ethanperrine"
    REPO = "REPO"
    BRANCH = "BRANCH"
    FILE = "FILE"
    TOKEN = "YOUR_PERSONAL_ACCESS_TOKEN"
    for proxy in list:
        headers = {
            "Accept": "application/vnd.github+json",
            "Authorization": f"Token {TOKEN}",
            "Content-Type": "application/json",
        }
        #get the SHA of the file
        url = f"https://api.github.com/repos/{OWNER}/{REPO}/contents/{FILE}?ref={BRANCH}"
        response = requests.get(url,headers = headers)
        if response.status_code != 200:
            print(f"Failed to get the sha of the file. Status Code: {response.status_code}")
            continue
        data = json.loads(response.text)
        sha = data["sha"]
        
        content = proxy + "\n"
        data = {
            "message": "Upload new proxy",
            "content": content,
            "sha": sha,
        }
        url = f"https://api.github.com/repos/{OWNER}/{REPO}/contents/{FILE}"

def scrape_proxies(website_sources: List[str]) -> Union[List[str], str]:
    proxies = []
    pattern = r"\b\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}:\d{1,5}\b"
    pattern_compiled = re.compile(pattern)
    for site in website_sources:
        try:
            response = requests.get(site)
            proxies.extend(pattern_compiled.findall(response.text))
        except:
            return "Could not scrape proxies."
    return proxies

def proxy_checker(proxies: str) -> str:
    try:
        custom_website = "https://www.google.com/"
        search_string = "I'm Feeling Lucky"

        user_agents = "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/109.0"
        response = requests.get(custom_website, proxies={'http': proxies, 'https': proxies}, timeout=2, headers={'User-Agent': user_agents})
        if response.status_code == 200:
            if search_string in response.text:
                return proxies
    except Exception:
        pass

def main():
    while True:
        new_list_of_proxies = []
        proxies = scrape_proxies(list_of_proxy_sources)
        with ThreadPoolExecutor(max_workers=500) as executor:
            futures = [executor.submit(proxy_checker, proxy) for proxy in proxies]
            for future in as_completed(futures):
                good_proxy = future.result()
                if good_proxy:
                    print(good_proxy)
                    new_list_of_proxies.append(good_proxy)
        with open('proxies.txt', 'w') as f:
            for proxy in new_list_of_proxies:
                f.write(f'{proxy}\n')

if __name__ == '__main__':
    main()

