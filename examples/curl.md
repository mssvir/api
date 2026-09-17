# MSSV API cURL Examples

Official website: https://mssv.ir/

Set your token locally. Do not commit it to source control.

```bash
export MSSV_API_TOKEN='YOUR_MSSV_API_TOKEN'
export MSSV_API_BASE='https://mssv.ir/api/machine/v1'
```

## Account

```bash
curl -sS "$MSSV_API_BASE/me" \
  -H "Authorization: Bearer $MSSV_API_TOKEN" \
  -H 'Accept: application/json'
```

## Products

```bash
curl -sS "$MSSV_API_BASE/products" \
  -H "Authorization: Bearer $MSSV_API_TOKEN" \
  -H 'Accept: application/json'
```

## List services

```bash
curl -sS "$MSSV_API_BASE/services?page=1&per_page=50" \
  -H "Authorization: Bearer $MSSV_API_TOKEN" \
  -H 'Accept: application/json'
```

## Service details

```bash
curl -sS "$MSSV_API_BASE/services/123" \
  -H "Authorization: Bearer $MSSV_API_TOKEN" \
  -H 'Accept: application/json'
```

## Restart a service

Only send an action that appears in the service's `available_actions` response.

```bash
curl -sS -X POST "$MSSV_API_BASE/services/123/action" \
  -H "Authorization: Bearer $MSSV_API_TOKEN" \
  -H 'Content-Type: application/json' \
  -H 'Idempotency-Key: service-123-restart-001' \
  --data '{"action":"restart"}'
```

## List invoices

```bash
curl -sS "$MSSV_API_BASE/invoices?page=1&per_page=50" \
  -H "Authorization: Bearer $MSSV_API_TOKEN" \
  -H 'Accept: application/json'
```

## Wallet history

```bash
curl -sS "$MSSV_API_BASE/wallet?page=1&per_page=50" \
  -H "Authorization: Bearer $MSSV_API_TOKEN" \
  -H 'Accept: application/json'
```

## Create a support ticket

```bash
curl -sS -X POST "$MSSV_API_BASE/tickets" \
  -H "Authorization: Bearer $MSSV_API_TOKEN" \
  -H 'Content-Type: application/json' \
  -H 'Idempotency-Key: ticket-create-001' \
  --data '{
    "title":"Service question",
    "message":"Please review my service.",
    "service_id":123,
    "priority":"Medium"
  }'
```

## Check verification status

```bash
curl -sS "$MSSV_API_BASE/verification" \
  -H "Authorization: Bearer $MSSV_API_TOKEN" \
  -H 'Accept: application/json'
```

For product, hosting and account access information, visit https://mssv.ir/.
