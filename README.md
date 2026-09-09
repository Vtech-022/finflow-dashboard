# FinFlow — Dashboard Financeiro

Dashboard financeiro pessoal (Entradas, Gastos, Investimentos e Relatórios), hospedado no GitHub Pages com **sincronização em tempo real** via Firebase Firestore: quem acessar o link vê as edições de quem mais está usando, instantaneamente.

## Como funciona

O site é um único arquivo `index.html` (HTML + CSS + JS, sem build). Os dados ficam em um documento do Firestore (`finflow_rooms/principal`) e todo navegador aberto no site escuta esse documento em tempo real (`onSnapshot`). Quando alguém adiciona uma entrada, gasto ou investimento, o app salva no Firestore e todos os outros navegadores abertos atualizam a tela na hora — sem precisar recarregar a página.

Se o Firebase não estiver configurado, o site continua funcionando normalmente, mas cada navegador guarda os dados só localmente (`localStorage`), sem compartilhar com ninguém — é o modo padrão até você preencher a configuração abaixo.

## Configurar a sincronização em tempo real (uma vez só)

1. Acesse https://console.firebase.google.com/ e crie um projeto novo (gratuito, plano Spark).
2. No menu lateral, vá em **Build → Firestore Database** → **Criar banco de dados** → modo **produção** → escolha uma região (ex: `southamerica-east1`).
3. Ainda no Firestore, abra a aba **Regras** e cole:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /finflow_rooms/principal {
         allow read, write: if true;
       }
     }
   }
   ```

   Isso libera leitura/escrita **só** para esse documento específico (o painel do FinFlow), não para o banco inteiro. Não é segurança de nível bancário — qualquer pessoa com o link do site consegue editar os dados —, mas é adequado para uso pessoal/casal. Se quiser mais proteção depois, dá para adicionar autenticação (posso te ajudar quando quiser).

4. No menu lateral, clique na engrenagem → **Configurações do projeto** → role até **Seus apps** → clique no ícone `</>` (Web) → registre um app (não precisa de Firebase Hosting).
5. Copie o objeto `firebaseConfig` que aparece (algo como abaixo) e me envie, ou edite você mesmo o arquivo `index.html` neste repositório (direto pelo GitHub, no navegador) substituindo o bloco `const firebaseConfig = {...}` perto do topo do `<script>` final:

   ```js
   const firebaseConfig = {
     apiKey: "...",
     authDomain: "...",
     projectId: "...",
     storageBucket: "...",
     messagingSenderId: "...",
     appId: "..."
   };
   ```

6. Salve e recarregue o site publicado — o indicador no topo do Dashboard deve mudar de "○ Somente local" para "● Sincronizado em tempo real".

## Hospedagem (GitHub Pages)

Este repositório já vem com um workflow (`.github/workflows/pages.yml`) que publica o site automaticamente a cada `push` na branch `main`. Só falta habilitar uma vez:

**Settings → Pages → Source → "GitHub Actions"**.

Depois disso, toda alteração enviada para `main` atualiza o site publicado em `https://<seu-usuario>.github.io/<nome-do-repositorio>/` em cerca de 1 minuto.

## Estrutura

- `index.html` — o site inteiro (interface, estilos e lógica).
- `.github/workflows/pages.yml` — publica o site no GitHub Pages a cada push.
