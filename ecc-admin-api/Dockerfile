# ──────────────────────────────────────────────
# Dockerfile — ecc-admin-api (Node.js / Express)
# ──────────────────────────────────────────────
FROM node:20-alpine

# Criar diretório da aplicação
WORKDIR /app

# Copiar manifesto de dependências e instalar apenas produção
COPY package*.json ./
RUN npm ci --omit=dev

# Copiar o código-fonte
COPY src/ ./src/

# Expor a porta definida no .env (padrão 3000)
EXPOSE 3000

# Iniciar o servidor
CMD ["node", "src/server.js"]
