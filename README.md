<div align="center">

<img src="https://img.shields.io/badge/MedLab_Digital-Grupo_AD-085041?style=for-the-badge&labelColor=04342C" alt="MedLab Digital">

# GastroHub
### Inteligência Clínica em Gastroenterologia

**Plataforma de monitoramento médico em tempo real — desenvolvida pelo Grupo AD**

[![Deploy](https://img.shields.io/badge/Deploy-Vercel-000000?style=flat-square&logo=vercel)](https://vercel.com)
[![Database](https://img.shields.io/badge/Database-Supabase-3ECF8E?style=flat-square&logo=supabase)](https://supabase.com)
[![Backend](https://img.shields.io/badge/Backend-Google_Apps_Script-4285F4?style=flat-square&logo=google)](https://script.google.com)
[![License](https://img.shields.io/badge/License-Propriet%C3%A1rio-red?style=flat-square)](LICENSE)

---

![GastroHub Preview](https://via.placeholder.com/900x500/04342C/1D9E75?text=GastroHub+·+MedLab+Digital)

</div>

---

## Sobre o produto

O **GastroHub** é uma plataforma SaaS voltada para **gastroenterologistas e clínicas especializadas**, que centraliza em um único dashboard todas as informações críticas da especialidade — atualizadas automaticamente, sem esforço manual.

Desenvolvido e mantido pelo **Grupo AD | Comunicação, Marketing e Desenvolvimento** sob a marca **MedLab Digital**.

---

## Funcionalidades

| Módulo | Descrição | Frequência |
|--------|-----------|------------|
| 📄 **Monitor de Diretrizes** | Scraping automático da FBG, SOBED e SBH — alerta quando nova diretriz, consenso ou nota técnica é publicada | Semanal |
| 🚨 **Recalls ANVISA** | Monitoramento de recolhimentos de medicamentos gastroenterológicos (IBPs, mesalazina, lactulose etc.) com alerta imediato por e-mail | Diário |
| 🔬 **PubMed Diário** | Consulta à API gratuita do PubMed com termos como Crohn, IBD, colonoscopy, H. pylori — entrega os 5 artigos mais recentes | Diário |
| 📅 **Calendário GI** | Todas as datas e campanhas de gastroenterologia do ano (Semana Azul, Dia Mundial das DII, Dia do Gastroenterologista etc.) com alertas de proximidade | Diário |
| 📈 **Trending Topics** | Monitoramento de termos em alta no Google Trends e Twitter/X relacionados à saúde digestiva — útil para pautar conteúdo nas redes sociais | Semanal |

---

## Stack tecnológica

```
Frontend         → HTML5 + CSS3 + JavaScript (Vanilla) — Single Page App
Banco de dados   → Supabase (PostgreSQL) — sa-east-1 (São Paulo)
Backend/Scraping → Google Apps Script (5 módulos com triggers automáticos)
Deploy           → Vercel
Fontes de dados  → FBG · SOBED · SBH · ANVISA · PubMed API · Google Trends RSS
```

---

## Arquitetura

```
┌─────────────────────────────────────────────────┐
│              Google Apps Script                  │
│                                                  │
│  ┌──────────┐ ┌────────┐ ┌────────┐ ┌────────┐  │
│  │Diretrizes│ │ANVISA  │ │PubMed  │ │Trending│  │
│  │(seg 07h) │ │(diário)│ │(diário)│ │(sex 8h)│  │
│  └────┬─────┘ └───┬────┘ └───┬────┘ └───┬────┘  │
│       └───────────┴──────────┴───────────┘       │
│                      │ doGet() /exec              │
└──────────────────────┼──────────────────────────-┘
                       │ REST API
         ┌─────────────▼─────────────┐
         │        Supabase           │
         │   (PostgreSQL + RLS)      │
         │                           │
         │  usuarios   diretrizes    │
         │  recalls    artigos_pubmed│
         │  calendario trending_topics│
         └─────────────┬─────────────┘
                       │ REST API (anon key)
         ┌─────────────▼─────────────┐
         │     GastroHub HTML        │
         │  (SPA · Vercel Deploy)    │
         │                           │
         │  Login → Dashboard →      │
         │  5 módulos + Config       │
         └───────────────────────────┘
```

---

## Estrutura do projeto

```
gastrohub/
├── gastrohub.html          # SPA completa (frontend + lógica)
├── index.html              # Redirect para gastrohub.html
├── vercel.json             # Configuração de deploy e headers
├── GastroHub_Scripts.gs    # Google Apps Script (5 módulos + triggers)
└── README.md
```

---

## Banco de dados (Supabase)

| Tabela | Descrição |
|--------|-----------|
| `usuarios` | Médicos/clínicas cadastrados (email, senha_hash, script_url) |
| `diretrizes` | Diretrizes FBG, SOBED, SBH com link e tipo |
| `recalls` | Recalls ANVISA com status ativo/encerrado |
| `artigos_pubmed` | Artigos indexados com PMID, tags e link |
| `calendario_gi` | Datas e campanhas anuais da especialidade |
| `trending_topics` | Termos em alta (Google Trends + Twitter/X) |
| `sync_logs` | Log de cada execução dos módulos |

**RLS ativo:** leitura pública via `anon key` · escrita exclusiva via `service_role` (Apps Script).

---

## Deploy

### Vercel (recomendado)

```bash
# Opção 1 — Upload direto
# Acesse vercel.com/new/upload e arraste o arquivo gastrohub-vercel.zip

# Opção 2 — CLI
npm i -g vercel
cd gastrohub-vercel/
vercel --prod
```

### Google Apps Script

1. Acesse [script.google.com](https://script.google.com) → Novo projeto
2. Cole o conteúdo de `GastroHub_Scripts.gs`
3. **Implantar → Nova implantação**
   - Tipo: Aplicativo da Web
   - Executar como: Eu mesmo
   - Acesso: Qualquer pessoa (anônimo)
4. Copie a URL `/exec` gerada
5. Execute `criarTriggers()` **uma única vez** para agendar os 5 módulos

---

## Variáveis de ambiente

Todas as credenciais estão embutidas no HTML para simplicidade de deploy.
Para produção com múltiplos clientes, recomenda-se externalizar via variáveis Vercel:

| Variável | Descrição |
|----------|-----------|
| `SUPA_URL` | URL do projeto Supabase |
| `SUPA_KEY` | Anon key pública do Supabase |
| `SCRIPT_URL` | URL do Web App do Google Apps Script |

---

## Acesso demo

```
URL:   https://gastrohub.vercel.app
Email: demo@gastrohub.com.br
Senha: gastro2024
```

---

## Roadmap

- [ ] Multi-tenant — painel administrativo para gerenciar múltiplas clínicas
- [ ] Notificações push via PWA
- [ ] Integração com WhatsApp Business API para alertas de recall
- [ ] Módulo de quiz clínico semanal
- [ ] Painel de indicadores de qualidade em endoscopia (taxa de detecção de adenoma, tempo de retirada)
- [ ] App mobile (React Native)
- [ ] White-label para outras especialidades (Cardiologia, Neurologia etc.)

---

## Licença

Produto proprietário — © 2026 **Grupo AD | Comunicação, Marketing e Desenvolvimento**.
Todos os direitos reservados. Proibida a reprodução ou redistribuição sem autorização expressa.

---

<div align="center">

Desenvolvido com dedicação pelo<br>
**Grupo AD · Comunicação, Marketing e Desenvolvimento**<br>
sob a marca **MedLab Digital**

*Tecnologia a serviço da medicina*

</div>
