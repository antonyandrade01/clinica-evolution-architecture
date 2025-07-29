<div align="center">
  <a href="README.md">Read this document in English 🇬🇧</a>
</div>

---

# 🏥 Clínica Evolution - Sistema de Gestão para Clínicas

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.9%2B-blue?style=for-the-badge&logo=python" alt="Python"/>
  <img src="https://img.shields.io/badge/Flask-2.0%2B-green?style=for-the-badge&logo=flask" alt="Flask"/>
  <img src="https://img.shields.io/badge/Docker-Ready-blue?style=for-the-badge&logo=docker" alt="Docker"/>
</p>

**Solução completa para gerenciamento de clínicas** com agendamento inteligente, prontuário eletrônico integrado e comunicação em tempo real. 


**Note:** Este repositório contém **a documentação da arquitetura e a configuração para o ambiente de desenvolvimento.** O código da aplicação está em um repositório privado. O objetivo deste projeto é demonstrar o design do sistema e habilidades de deploy com docker.

## ✨ Funcionalidades Principais

*   **📅 Agenda Inteligente:** Visualização em calendário (mês/semana/dia) ou lista, cores por status, edição em tempo real e filtros avançados.
*   **👥 Gestão de Usuários Multiperfil:** 4 níveis de acesso (Admin, Recepcionista, Profissional, Paciente) com autenticação segura (CPF/E-mail) e proteção CAPTCHA.
*   **📄 Prontuário Eletrônico (PEP):** Editor de documentos **ONLYOFFICE** integrado para DOCX/XLSX, com controle de versões e organização por pastas.
*   **🔔 Comunicação em Tempo Real:** Notificações instantâneas via WebSockets (Socket.IO) sobre eventos importantes na plataforma.

---

## 🚀 Guia de Início Rápido (Ambiente de Desenvolvimento)

O ambiente é 100% containerizado com Docker, garantindo uma instalação rápida e consistente.

**1. Clone o Repositório:**
```
bash
git clone https://github.com/antonyandrade01/clinica-evolution-architecture.git
cd clinica-evolution-architecture
```

**2. Configure o Ambiente:**
Copie o arquivo de exemplo. As senhas padrão já estão configuradas para facilitar.
```
bash
cp .env.example .env
```

**3. Inicie os Serviços:**
Este comando irá construir, baixar e iniciar todos os containers necessários.
```
bash
docker-compose up -d --build
```

**4. Carregue os Dados de Demonstração (Opcional):**
Para popular o sistema com dados de teste, execute o comando abaixo para restaurar o backup padrão.
```
bash
docker exec -i clinica_mariadb_db mariadb -u evolution -p'#!Ev0_Lm90=4M4' clinica_evolution < backup.sql
```

**5. Acesse as Interfaces:**
*   **Aplicação Principal:** 👉 **[http://localhost:5000](http://localhost:5000)**
*   **Interface Gráfica do Banco (Adminer):** 👉 **[http://localhost:8082](http://localhost:8082)**
*   **Servidor ONLYOFFICE:** 👉 [http://localhost:8081](http://localhost:8081)

---

## 🔑 Credenciais de Acesso (Dados de Teste)

| Perfil        | E-mail                  | Senha      |
|---------------|-------------------------|------------|
| Administrador | `admin@clinica.com`     | `senha123` |
| Recepcionista | `recepcao@clinica.com`  | `recep123` |
| Profissional  | `maria@clinica.com`     | `maria123` |
| Paciente      | `joao@exemplo.com`      | `joao123`  |

---

## 🔧 Manutenção e Operações

### Backup e Restauração do Banco de Dados

- **Criar um Backup:**
  ```
  bash
  docker exec clinica_mariadb_db mariadb-dump -u evolution -p'#!Ev0_Lm90=4M4' clinica_evolution > backup_$(date +%d-%m-%Y).sql
  ```
- **Restaurar um Backup:**
  ```
  bash
  docker exec -i clinica_mariadb_db mariadb -u evolution -p'#!Ev0_Lm90=4M4' clinica_evolution < nome_do_arquivo_backup.sql
  ```

### Comandos Úteis do Docker
| Ação                       | Comando                                      |
|----------------------------|----------------------------------------------|
| **Parar todos os serviços**| `docker-compose down`                        |
| **Ver logs da aplicação**  | `docker-compose logs -f flask_app`           |
| **Reiniciar a aplicação**  | `docker-compose restart flask_app`           |

---

## 🛠️ Arquitetura e Tecnologias

#### Diagrama da Arquitetura de Containers
<p align="center">
  <img src="https://github.com/antonyandrade01/clinica-evolution-architecture/blob/main/docs/images/arquitetura.png?raw=true" alt="Diagrama da Arquitetura de Containers" width="80%">
</p>

| Camada         | Tecnologias                                                                 |
|----------------|-----------------------------------------------------------------------------|
| **Backend**    | Python 3, Flask, SQLAlchemy, Flask-SocketIO, Gunicorn                        |
| **Frontend**   | Bootstrap 5, JavaScript, FullCalendar.js, SweetAlert2                        |
| **Database**   | MariaDB                                                                      |
| **Infrastructure** | Docker, Docker Compose                                                     |
| **Integrations** | ONLYOFFICE Docs, Redis, RabbitMQ, Adminer                                  |

---
### Otimizações e Padrões de DevOps Implementados

Além da arquitetura base, este projeto incorpora padrões profissionais de DevOps para garantir segurança, eficiência e automação:

*   **🚀 Imagens Otimizadas com Multi-Stage Builds:** A imagem Docker da aplicação Flask é construída usando um processo de múltiplos estágios. Isso separa as dependências de build das de runtime, resultando em uma imagem final menor e mais rápida.

*   **🛡️ Segurança com Usuário Não-Root:** A aplicação no contêiner é executada por um usuário dedicado (`appuser`), em vez de rodar como `root`. Esta é uma prática de segurança implementada para mitigar riscos em caso de uma vulnerabilidade.

*   **⚙️ Backups Automatizados com Ofelia:** Um serviço dedicado (`Ofelia`) foi adicionado para orquestrar backups automáticos e agendados da base de dados e dos volumes de dados, utilizando `rclone` para enviar os arquivos para um armazenamento em nuvem (Google Drive), incluindo uma política de retenção inteligente.

*   **▶️ Inicialização Robusta com Entrypoint:** Um script de `entrypoint.sh` garante que tarefas essenciais, como a aplicação de migrações do banco de dados (`flask db upgrade`) e o ajuste de permissões de volumes, sejam executadas antes que a aplicação principal seja iniciada, evitando erros e garantindo a consistência do ambiente.
<details>
  <summary>Clique para ver o Dockerfile.flask otimizado</summary>

  ```dockerfile
# --- Estágio de Build ---
FROM python:3.13.5-alpine3.22 AS builder

# Define o diretório de trabalho
WORKDIR /app

# Instala dependências do sistema
RUN apk add --no-cache gcc musl-dev linux-headers

# Ambiente virtual para isolar as dependências da aplicação
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

# Instala as dependências no ambiente virtual
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copia o restante do código da aplicação
COPY . .

# --- Estágio de Runtime ---
FROM python:3.13.5-alpine3.22 AS runtime

# Cria um usuário não-root e um grupo
RUN apk add --no-cache su-exec curl && \
    addgroup -S appgroup && \
    adduser -S appuser -G appgroup

# Define o diretório de trabalho
WORKDIR /app
COPY --from=builder /opt/venv /opt/venv

# Copia o código da aplicação do estágio de build
COPY --from=builder --chown=appuser:appgroup /app /app

# Copia o script de entrypoint para dentro da imagem
COPY ./docker/entrypoint.sh /usr/local/bin/
RUN chmod +x /usr/local/bin/entrypoint.sh
ENTRYPOINT ["entrypoint.sh"]

# Define as variáveis de ambiente para usar o ambiente virtual
ENV PATH="/opt/venv/bin:$PATH"
ENV PYTHONUNBUFFERED=1

EXPOSE 5000

CMD ["gunicorn", "--workers", "2", "--worker-class", "eventlet", "--bind", "0.0.0.0:5000", "app:app"]
```
</details>

---

## 👤 Contato

Criado por **Antony Andrade** - Vamos nos conectar!

<p>
  <a href="https://github.com/antonyandrade01" target="_blank"><img src="https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white" alt="GitHub"/></a>
  <a href="https://www.linkedin.com/in/antony-andrade-a04b92b7/" target="_blank"><img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white" alt="LinkedIn"/></a>
</p>
