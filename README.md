# Eco: monitor pessoal de menções

Busca menções reais das suas marcas, classifica o sentimento, sugere respostas e gera um `dashboard.html` local. Só usa a biblioteca padrão do Python 3.9+ (sem `pip install`).

## 1. Configurar as marcas

Edite `config.json`:

```json
{
  "days": 7,
  "brands": [
    {"name": "Seu Nome", "aliases": ["@seuarroba", "seusite.com"], "exclude": ["homônimo"], "require": []}
  ],
  "feeds": [
    {"name": "Nome do podcast", "type": "Podcast", "url": "https://.../feed.xml"}
  ]
}
```

- `aliases`: outras formas de citarem você (arroba, site, apelido).
- `exclude`: palavras que descartam a menção (evita homônimos).
- `require`: se preenchido, a menção precisa conter pelo menos uma dessas palavras (útil para nomes comuns, por exemplo `["design", "freelancer"]`).
- `feeds`: RSS de podcasts, newsletters e blogs que você quer vigiar. O Eco só lê o texto do feed (título e descrição), não transcreve áudio.

## 2. Rodar (PowerShell, na pasta do projeto)

```powershell
python eco.py run --open
```

Sem IA (palavras-chave): `python eco.py run --no-ai`.

## 3. IA para sentimento e respostas (opcional)

Crie uma chave em console.anthropic.com (a API é cobrada à parte da assinatura do Claude) e defina:

```powershell
setx ANTHROPIC_API_KEY "sua-chave"
```

Feche e abra o terminal. Sem a chave, o Eco classifica por palavras-chave e usa respostas-modelo. O modelo pode ser trocado com a variável `ECO_MODEL`. `--limit N` controla quantas menções novas por marca vão para a IA a cada execução (padrão 40).

## 4. Automatizar todo dia às 8h

```powershell
schtasks /Create /SC DAILY /ST 08:00 /TN EcoMonitor /TR "python C:\caminho\eco-monitor\eco.py run"
```

Se `python` não for reconhecido pelo agendador, use o caminho completo de `where python`.

## Fontes e limites

- Google Notícias (RSS), Hacker News (API pública), Reddit (RSS) e os feeds que você configurar.
- Instagram, X, LinkedIn e TikTok não entram: não oferecem busca pública gratuita e estável.
- O Reddit às vezes bloqueia pedidos automáticos; nesse caso o Eco avisa e segue com as outras fontes.
- O texto das menções vem da internet e é tratado como dado, mas a IA pode errar. Revise antes de responder.
- Os dados ficam em `eco.db` na sua máquina. O marcador "resolvida" fica no navegador.
