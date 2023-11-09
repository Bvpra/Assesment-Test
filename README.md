                   First Task
            ------------------------

Travel-advisory API and saving the data to a file, you can follow these steps:

---> Install the requests library (if you haven't already) to make HTTP requests

# pip install requests

---> Create a Python script, e.g., country_lookup.py, and add the following code

import requests
import json
import argparse

API_URL = "https://www.travel-advisory.info/api"
DATA_FILE = "data.json"

def fetch_data():
    response = requests.get(API_URL)
    if response.status_code == 200:
        data = response.json()
        with open(DATA_FILE, "w") as file:
            json.dump(data, file)
        return data
    else:
        print("Failed to fetch data from the API.")
        return None

def lookup_country(country_codes):
    try:
        with open(DATA_FILE, "r") as file:
            data = json.load(file)

        countries = data.get("data", {}).get("names", {})
        for code in country_codes:
            country_name = countries.get(code, "Country not found")
            print(f"{code}: {country_name}")
    except FileNotFoundError:
        print("Data file 'data.json' not found. Please run 'fetch' command first.")

if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="Country Lookup CLI")
    parser.add_argument("--fetch", action="store_true", help="Fetch data from API and save to data.json")
    parser.add_argument("--countryCode", nargs="+", help="Country codes to lookup")

    args = parser.parse_args()

    if args.fetch:
        fetch_data()
    if args.countryCode:
        lookup_country(args.countryCode)



 ----> fetch: Fetch data from the API and save it to a file.
 ----> countryCode: Provide one or more country codes to look up the country names.

To use the CLI:

To fetch data: python country_lookup.py --fetch
To lookup country names: python country_lookup.py --countryCode AU US (you can specify multiple country codes)

Note: Before running the script, ensure you have the required packages installed, and you should have a data.json file in the same directory where the script is located.


                                          2nd Task
                                      ---------------

1.Convert code to a REST service.
2.Set up a local Kubernetes cluster.
3.Deploy the service to the local Kubernetes cluster.
4.Set up basic monitoring.

---> Let's create an Ansible playbook for these tasks:


---
- name: Deploy County Code Lookup Service
  hosts: localhost
  gather_facts: false

  tasks:
    - name: Install required packages
      become: true
      apt:
        name: "{{ item }}"
        state: present
      loop:
        - python3-pip
        - docker
        - docker-compose

    - name: Install required Python packages
      become: true
      pip:
        name: "{{ item }}"
      loop:
        - Flask
        - requests

    - name: Set up local Kubernetes cluster (using Minikube)
      become: true
      shell: minikube start
      ignore_errors: yes

    - name: Build Docker image for County Code Lookup Service
      become: true
      shell: "docker build -t county-code-service ."
      args:
        chdir: /path/to/your/service

    - name: Deploy County Code Lookup Service to local Kubernetes
      become: true
      shell: kubectl apply -f /path/to/your/k8s/deployment.yaml
      ignore_errors: yes

    - name: Wait for service to be ready
      become: true
      shell: kubectl wait --for=condition=available deployment/county-code-service --timeout=300s

    - name: Set up basic monitoring (using Prometheus and Grafana)
      become: true
      shell: "kubectl apply -f /path/to/your/monitoring/manifests"
      ignore_errors: yes
      
    - name: Check status of the external API
      uri:
        url: "https://www.travel-advisory.info/api"
        return_content: yes
      register: api_response

    - name: Print API status
      debug:
        var: api_response.json.api_status

  # You can use api_response.json.api_status in subsequent tasks as needed













