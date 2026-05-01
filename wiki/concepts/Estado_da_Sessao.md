---
title: Estado da Sessão
tags: [conceito, infraestrutura]
status: semente
date: 2026-05-01
---

# Estado da Sessão (Session State)

O estado da sessão são os dados mantidos pela aplicação sobre a interação de um usuário específico durante um período de tempo.

## Estratégias de Armazenamento
1. **Lado do Cliente (Client Side)**: Armazenar o estado em cookies, campos de formulário ocultos ou URLs. Bom para escalabilidade, mas possui limites de tamanho e segurança.
2. **Lado do Servidor em Memória (Server Side Memory)**: Armazenar o estado na memória do servidor web (ex: `HttpSession`). Simples, mas difícil de escalar (requer "sessões pegajosas" ou sticky sessions).
3. **Banco de Dados/Armazenamento Externo**: Armazenar o estado em um banco de dados ou cache (Redis). Mais robusto e escalável, mas adiciona latência e complexidade.

## Princípio Chave
Mantenha o estado da sessão o menor possível. Prefira designs sem estado (stateless) sempre que for prático.

## Fonte
- [[Livro_PEAA]] (Capítulo 6)
