diff --git a/README.md b/README.md
index d44ef46faf6d8f541dfd9c701fff8e7ac2cd8be5..dfea085a61bd75c6f0ccf8befd9c503654b11703 100644
--- a/README.md
+++ b/README.md
@@ -1,2 +1,257 @@
-# Estoque
-Controle de estoque
+# Sistema de Controle de Estoque (Loja Pequena)
+
+Este repositório agora descreve um **MVP (produto mínimo viável)** para uma loja pequena (pipa, linha, rabiola, carretilha etc.), com foco em:
+
+- controle de entradas e saídas;
+- leitura de código de barras;
+- consulta rápida via celular;
+- apoio à compra de mercadorias sem precisar ir até a loja para conferir estoque.
+
+---
+
+## 1) Problema que vamos resolver
+
+Hoje, quando falta mercadoria, vocês precisam:
+
+1. contar na mão na loja, ou
+2. ir até a loja para conferir.
+
+Com o sistema, você terá uma tela no celular para ver:
+
+- quantidade atual por item;
+- itens abaixo do estoque mínimo;
+- histórico de movimentações (entrada e venda/saída).
+
+---
+
+## 2) Escopo do MVP (comece simples)
+
+Como você é Dev Jr (e está aprendendo), foque primeiro no que entrega valor rápido.
+
+### Funcionalidades essenciais (Sprint 1)
+
+1. **Cadastro de produto**
+   - nome (ex.: “Linha 10 chilena”)
+   - SKU interno (código único)
+   - código de barras (EAN/UPC, opcional no início)
+   - categoria (pipa, linha, rabiola...)
+   - estoque atual
+   - estoque mínimo
+   - custo e preço de venda
+
+2. **Movimentação de estoque**
+   - Entrada (compra de fornecedor)
+   - Saída (venda/perda/ajuste)
+   - Data/hora, usuário e observação
+
+3. **Consulta via celular (PWA ou responsivo)**
+   - lista de produtos
+   - busca por nome/SKU/código de barras
+   - alerta de baixo estoque
+
+4. **Leitor de código de barras**
+   - usar câmera do celular para ler (bibliotecas JS)
+   - ao ler, abrir produto e permitir lançar entrada/saída
+
+---
+
+## 3) Arquitetura simples recomendada
+
+Para começar sem complicar:
+
+- **Frontend Web responsivo** (funciona no desktop e celular)
+- **Backend API REST**
+- **Banco relacional** (PostgreSQL ou SQLite no início)
+
+### Stack sugerida para aprendizado rápido
+
+- Frontend: React + Vite + Tailwind (ou Bootstrap)
+- Backend: Node.js + Express + Prisma
+- Banco: PostgreSQL
+- Autenticação: JWT simples
+
+> Se quiser ainda mais simples no começo: backend com SQLite local e depois migra para PostgreSQL.
+
+---
+
+## 4) Modelagem inicial de banco (tabelas)
+
+### `products`
+- `id`
+- `name`
+- `sku` (único)
+- `barcode` (único, pode ser nulo)
+- `category`
+- `stock_current`
+- `stock_min`
+- `cost_price`
+- `sale_price`
+- `active`
+- `created_at`
+
+### `stock_movements`
+- `id`
+- `product_id` (FK products)
+- `type` (`IN`, `OUT`, `ADJUSTMENT`)
+- `quantity`
+- `reason` (compra, venda, perda, acerto)
+- `note`
+- `created_by`
+- `created_at`
+
+### `users`
+- `id`
+- `name`
+- `email`
+- `password_hash`
+- `role` (`ADMIN`, `EMPLOYEE`)
+
+---
+
+## 5) Regras de negócio importantes
+
+1. Nunca permitir estoque negativo (exceto se você decidir permitir com permissão de admin).
+2. Toda alteração manual deve gerar movimentação (rastreabilidade).
+3. Produto com `stock_current <= stock_min` deve entrar em alerta.
+4. Código de barras deve ser único por produto.
+
+---
+
+## 6) Fluxos práticos do dia a dia
+
+### Fluxo A — Compra de mercadoria
+
+1. Você está na rua e abre o sistema no celular.
+2. Vai em “Baixo estoque”.
+3. Sistema mostra: “Linha 10 (2 un), Rabiola fina (0 un)”.
+4. Você compra baseado nessa lista.
+5. Ao chegar na loja, lança entrada por leitura de código de barras.
+
+### Fluxo B — Venda no balcão
+
+1. Escaneia o produto com celular.
+2. Sistema localiza item.
+3. Registra saída de 1 unidade.
+4. Estoque atualiza na hora.
+
+---
+
+## 7) Endpoints iniciais (exemplo)
+
+- `POST /auth/login`
+- `GET /products?search=`
+- `POST /products`
+- `PATCH /products/:id`
+- `GET /products/low-stock`
+- `POST /movements`
+- `GET /movements?productId=`
+
+Exemplo de payload para movimentação:
+
+```json
+{
+  "productId": "uuid",
+  "type": "OUT",
+  "quantity": 2,
+  "reason": "VENDA",
+  "note": "Venda balcão"
+}
+```
+
+---
+
+## 8) Leitura de código de barras (câmera)
+
+Você pode usar bibliotecas como:
+
+- `@zxing/browser`
+- `quagga2`
+
+### Passos simples
+
+1. pedir permissão de câmera;
+2. ler frame e detectar código;
+3. chamar `GET /products?search=<codigo>`;
+4. se encontrado, abrir tela do produto;
+5. registrar entrada/saída.
+
+---
+
+## 9) Acesso via celular (sem precisar app nativo)
+
+Para não complicar no início, faça uma **PWA**:
+
+- interface responsiva;
+- “Adicionar à tela inicial” no Android/iPhone;
+- funciona como “app” sem publicar na loja.
+
+---
+
+## 10) Plano de implementação para Dev Jr (passo a passo)
+
+### Semana 1
+- criar projeto backend e banco;
+- modelar tabelas;
+- implementar CRUD de produtos.
+
+### Semana 2
+- implementar movimentações de estoque;
+- criar regra de não permitir estoque negativo;
+- criar endpoint de baixo estoque.
+
+### Semana 3
+- criar frontend responsivo com login;
+- lista de produtos e tela de detalhes.
+
+### Semana 4
+- integrar leitor de código de barras;
+- melhorar UX no celular;
+- preparar deploy.
+
+---
+
+## 11) Exemplo de itens reais da loja (seed inicial)
+
+- Pipa tradicional 50cm
+- Linha 10 chilena
+- Rabiola colorida
+- Lata de linha encerada
+- Carretilha simples
+
+Para cada item, já defina:
+
+- estoque mínimo;
+- fornecedor principal;
+- tempo médio de reposição.
+
+---
+
+## 12) Próximos incrementos (depois do MVP)
+
+- relatório de produtos mais vendidos;
+- sugestão automática de compra;
+- integração com emissão de venda;
+- multiusuário com níveis de acesso mais completos.
+
+---
+
+## 13) Dicas de aprendizado (importante)
+
+Como Dev Jr, foque em:
+
+1. primeiro fazer funcionar (simples e claro);
+2. depois refatorar;
+3. escrever pequenos testes para regras críticas (ex.: estoque negativo);
+4. usar commits pequenos e descritivos.
+
+Exemplo de commit bom:
+
+- `feat(stock): cria endpoint de movimentação de entrada e saída`
+
+---
+
+Se quiser, no próximo passo posso transformar isso em:
+
+1. estrutura de pastas;
+2. schema Prisma inicial;
+3. primeiros endpoints prontos para você começar a codar.
