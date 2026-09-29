# api-solda-loja

Backend do checkout Pix da **Máquina Inversora de Solda MIG 130A 3 em 1 (Kit Completo)** — gateway ADEX (`https://api.adex.cash/functions/v1`). Guarda as chaves da ADEX e o preço. O navegador nunca vê as chaves e nunca decide o valor.

## Oferta (definida em `server.js`)

| Item | Valor |
|---|---|
| Preço unitário | R$ 109,90 (110V ou 220V) |
| Quantidade | 1 a 5 |
| Frete | Grátis |

## Rotas

| Método | Rota | O que faz |
|---|---|---|
| POST | `/api/pix` | Valida dados + item (`{ voltage, quantity }`), cria a cobrança na ADEX, devolve QR Code e copia-e-cola |
| GET | `/api/pix/:id/status` | Consulta o status (a página faz polling a cada 5s) |
| POST | `/api/webhook` | Recebe avisos da ADEX (`charge.paid` etc.), valida `x-webhook-signature` |
| GET | `/health` | Health check (mostra se a ADEX já está configurada) |

## Variáveis de ambiente (Railway → Variables)

Veja `.env.example`. Obrigatórias: `ADEX_PUBLIC_KEY`, `ADEX_SECRET_KEY`, `PORT=8080`. Recomendadas: `ADEX_WEBHOOK_SECRET`, `PUBLIC_API_URL`.
Sem as chaves a API sobe normalmente, mas `/api/pix` responde 503 até você preencher.

## Rodar local

```bash
npm install
cp .env.example .env   # preencha (ou MOCK_ADEX=true)
npm run dev
```

## Deploy no Railway

1. New Project → Deploy from GitHub repo → `ballinbsn/api-solda-loja`.
2. Variables: preencha `ADEX_PUBLIC_KEY` e `ADEX_SECRET_KEY`.
3. Settings → Networking → Generate Domain (porta 8080). Essa URL vai em `PUBLIC_API_URL` e em `API_URL` (`assets/checkout/config.js` do site).
4. Painel ADEX → Webhooks: cadastre `https://SUA-URL/api/webhook` e copie o segredo para `ADEX_WEBHOOK_SECRET`.
5. Faça uma compra de teste e confira o valor cobrado.
