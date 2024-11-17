# Bootcamp-Microsoft-Certification-Challenge-1-AI_102

## Projeto - Tradução de Artigos

### Colab code

- Bibliotecas

```
from bs4 import BeautifulSoup
import requests, uuid, json
import os
from dotenv import load_dotenv
```

- Variáveis de Ambiente
```
API_KEY = os.getenv("AZURE_OPENAI_KEY")
API_ENDPOINT = os.getenv("AZURE_ENDPOINT")
key = os.getenv("TRANSLATOR_API_KEY")
endpoint = os.getenv("TRANSLATOR_ENDPOINT")
location = os.getenv("TRANSLATOR_LOCATION")
```

- Função de Extração de Texto
```
url="https://dev.to/arindam_1729/9-open-source-libraries-to-supercharge-your-next-project-c71"
def extract_text(url):
    response = requests.get(url)
    if response.status_code == 200:
        soup = BeautifulSoup(response.text, 'html.parser')
        for script in soup(["script", "style"]):
            script.decompose()
        text = soup.get_text(" ", strip=True)
        return text
    else:
        print("URL não encontrado. Status de resposta:", response.status_code)
        return None
extract_text(url)
```

- Função de Tradução de Texto
```
def translate_article(text, lang):
    headers = {
    "Content-Type": "application/json",
    "api-key": API_KEY,
    }
    
    # Payload for the request
    payload = {
      "messages": [
        {
          "role": "system",
          "content": [
            {
              "type": "text",
              "text": "Atue como um tradutor de textos"
            }
          ]
        },
        {
          "role": "user",
          "content": [
            {
              "type": "text",
              "text": f"traduza: {text} para o idioma {lang} e responda apenas com a tradução no formato markdown"
            }
          ]
        },    
      ],
      "temperature": 0.7,
      "top_p": 0.95,
      "max_tokens": 900
    }
    
    ENDPOINT = API_ENDPOINT
    
    # Send request
    try:
        response = requests.post(ENDPOINT, headers=headers, json=payload)
        response.raise_for_status()  # Will raise an HTTPError if the HTTP request returned an unsuccessful status code
    except requests.RequestException as e:
        raise SystemExit(f"Failed to make the request. Error: {e}")
    
    # Handle the response as needed (e.g., print or process)
    return (response.json()['choices'][0]['message']['content'])
```
