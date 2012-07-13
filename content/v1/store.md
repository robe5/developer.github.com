---
title: Store | ThreeSellers API
---

# Store API

## Obtener la cuenta

    GET /api/v1/store

### Request
    curl -u "usuario:contraseña" http://login.3sellers.com/api/v1/store.json

### Response

<%= headers 200 %>
<%= json :store %>