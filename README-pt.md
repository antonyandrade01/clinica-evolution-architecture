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

**Solução completa para gerenciamento de clínicas** com agendamento inteligente, prontuário eletrônico integrado e comunicação em tempo real. Este repositório contém a documentação da arquitetura e a configuração para o ambiente de desenvolvimento.

## ✨ Funcionalidades Principais

*   **📅 Agenda Inteligente:** Visualização em calendário (mês/semana/dia) ou lista, cores por status, edição em tempo real e filtros avançados.
*   **👥 Gestão de Usuários Multiperfil:** 4 níveis de acesso (Admin, Recepcionista, Profissional, Paciente) com autenticação segura (CPF/E-mail) e proteção CAPTCHA.
*   **📄 Prontuário Eletrônico (PEP):** Editor de documentos **ONLYOFFICE** integrado para DOCX/XLSX, com controle de versões e organização por pastas.
*   **🔔 Comunicação em Tempo Real:** Notificações instantâneas via WebSockets (Socket.IO) sobre eventos importantes na plataforma.

---

## 🚀 Guia de Início Rápido (Ambiente de Desenvolvimento)

O ambiente é 100% containerizado com Docker, garantindo uma instalação rápida e consistente.

**1. Clone o Repositório:**
```bash
git clone https://github.com/antonyandrade01/clinica-evolution-architecture.git
cd clinica-evolution-architecture
```

**2. Configure o Ambiente:**
Copie o arquivo de exemplo. As senhas padrão já estão configuradas para facilitar.
```bash
cp .env.example .env
```

**3. Inicie os Serviços:**
Este comando irá construir, baixar e iniciar todos os containers necessários.
```bash
docker-compose up -d --build
```

**4. Carregue os Dados de Demonstração (Opcional):**
Para popular o sistema com dados de teste, execute o comando abaixo para restaurar o backup padrão.
```bash
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
  ```bash
  docker exec clinica_mariadb_db mariadb-dump -u evolution -p'#!Ev0_Lm90=4M4' clinica_evolution > backup_$(date +%d-%m-%Y).sql
  ```
- **Restaurar um Backup:**
  ```bash
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
  <img src="https://github.com/antonyandrade01/clinica-evolution-architecture/blob/main/docs/images/arquitetura.png?raw=true" alt="Docker Architecture Diagram" width="80%">
</p>

| Camada         | Tecnologias                                                                 |
|----------------|-----------------------------------------------------------------------------|
| **Backend**    | Python 3, Flask, SQLAlchemy, Flask-SocketIO, Gunicorn                        |
| **Frontend**   | Bootstrap 5, JavaScript, FullCalendar.js, SweetAlert2                        |
| **Database**   | MariaDB                                                                      |
| **Infrastructure** | Docker, Docker Compose                                                     |
| **Integrations** | ONLYOFFICE Docs, Redis, RabbitMQ, Adminer                                  |

---
> **Clínica Evolution** - Modernizando a gestão de saúde através da tecnologia ✨  
> Desenvolvido com ❤️ por Antony Andrade
