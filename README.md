Para sincronizar o desenvolvimento de workflows do n8n entre múltiplos computadores via Git, a melhor estratégia é separar os dados persistentes locais dos dados versionáveis:

    Ativos no Git: Arquivos de entrada/saída (data/files/), scripts, exportações JSON dos workflows (workflows/) e os arquivos de configuração do Docker.

    Dados do Banco/Containers: Ficam mapeados via Bind Mount local (data/postgres/ e data/n8n/) e são ignorados pelo .gitignore para não inflar o repositório nem gerar conflitos de ID ou credenciais no Git.
    
Estrutura do Projeto
Plaintext

n8n-project/
├── docker-compose.yml
├── .env
├── .gitignore
├── data/
│   └── files/         ← Arquivos lidos/escritos pelos workflows (versionados)
├── workflows/         ← Exportações JSON dos seus workflows (versionados)
└── README.md

1.Criar o arquivo .gitignore:Evita versionar bancos de dados e chaves sensíveis.No diretório raiz do seu projeto, crie o arquivo .gitignore:gitignore# Dados locais do Postgres e n8n (não versionar no Git)
data/postgres/
data/n8n/

# Variáveis de ambiente com senhas e chaves de criptografia
.env

# Arquivos do sistema operacional
.DS_Store
Thumbs.db

2.Configurar as Variáveis de Ambiente (.env):Centraliza credenciais e chave de criptografia do n8n.Crie o arquivo .env na raiz. A variável N8N_ENCRYPTION_KEY é fundamental: se ela mudar entre computadores, o n8n não conseguirá abrir credenciais salvas no banco.env# Postgres Configuration
POSTGRES_USER=n8n_user
POSTGRES_PASSWORD=n8n_secure_password
POSTGRES_DB=n8n_db

# n8n Configuration
N8N_HOST=localhost
N8N_PORT=5678
N8N_PROTOCOL=http
WEBHOOK_URL=http://localhost:5678/

# Chave fixa para manter credenciais compatíveis em qualquer máquina
N8N_ENCRYPTION_KEY=sua_chave_secreta_super_segura_12345


4.Iniciar o ambiente e testar a leitura/escrita:

Suba os containers e acesse os arquivos.

Suba o ambiente via terminal:
docker compose up -d

Acesse no navegador: http://localhost:5678

Para ler ou salvar arquivos no nó Read/Write Files from Disk dentro do n8n, utilize sempre caminhos dentro da pasta mapeada:
Caminho no nó do n8n: /data/files/meu_arquivo.csv ou /data/files/relatorios/saida.json

    
