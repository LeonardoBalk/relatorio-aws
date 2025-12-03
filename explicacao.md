# Explicação do Trabalho Final de Computação em Nuvem (AWS)

## 🎯 Objetivo Principal

Este trabalho é uma **Prova de Conceito (POC)** para migrar o sistema de registros de alunos de uma universidade para a nuvem AWS. O objetivo é criar uma aplicação web que seja:

- **Altamente disponível** - funciona mesmo se parte do sistema falhar
- **Escalável** - cresce automaticamente conforme a demanda
- **Segura** - protege os dados dos alunos
- **Econômica** - usa recursos de forma eficiente

---

## 🏗️ Arquitetura e Componentes

### 1. **VPC (Virtual Private Cloud)**
É sua "rede privada virtual" dentro da AWS. Pense como um prédio isolado onde você controla quem entra e sai.
- **CIDR 10.0.0.0/16** = define o intervalo de IPs disponíveis (65.536 endereços)

### 2. **Subnets (Sub-redes)**
São divisões dentro da VPC:
- **Subnets Públicas** (10.0.1.0/24 e 10.0.2.0/24): Recursos acessíveis pela internet (servidores web)
- **Subnets Privadas** (10.0.3.0/24 e 10.0.4.0/24): Recursos protegidos (banco de dados)

### 3. **Internet Gateway (IGW)**
É a "porta de entrada" que conecta sua VPC à internet. Sem ele, ninguém de fora acessa seus servidores.

### 4. **EC2 (Elastic Compute Cloud)**
São os **servidores virtuais** onde a aplicação Node.js roda.
- **t3.micro**: tipo de instância pequena e barata (2 vCPUs, 1GB RAM)
- Roda a aplicação web na porta 80

### 5. **RDS (Relational Database Service)**
É o **banco de dados MySQL gerenciado** pela AWS.
- **db.t3.micro**: instância pequena de banco de dados
- Armazena os dados dos alunos
- Fica em subnet privada (mais seguro)
- AWS cuida de backups, atualizações e manutenção

### 6. **Application Load Balancer (ALB)**
É o **balanceador de carga** que distribui o tráfego entre múltiplos servidores.
- Usuário acessa o ALB → ALB direciona para um servidor disponível
- Se um servidor cair, o ALB para de enviar tráfego para ele

### 7. **Auto Scaling Group (ASG)**
**Escala automaticamente** o número de servidores:
- Mínimo: 2 instâncias (sempre rodando)
- Máximo: 6 instâncias (em picos de uso)
- Quando a CPU passa de 50%, cria mais servidores automaticamente

### 8. **Security Groups**
São **firewalls virtuais** que controlam o tráfego:
- **ALB-SG**: permite HTTP (porta 80) de qualquer lugar
- **WebServer-SG**: permite HTTP do ALB e SSH do seu IP
- **RDS-SG**: permite MySQL (porta 3306) apenas dos servidores web

### 9. **Secrets Manager**
Armazena **credenciais de forma segura** (usuário/senha do banco).
- Aplicação busca as credenciais em tempo de execução
- Evita senhas hardcoded no código

### 10. **Cloud9**
É um **ambiente de desenvolvimento** dentro da AWS.
- IDE no navegador para escrever código
- Acesso direto aos recursos AWS

### 11. **IAM (Identity and Access Management)**
Controla **permissões e acessos**:
- **LabRole**: permissões para recursos AWS
- **LabInstanceProfile**: permite que EC2 acesse outros serviços AWS

---

## 📊 Fases do Projeto

| Fase | O que faz |
|------|-----------|
| **Fase 1** | Planejamento - diagrama e custos |
| **Fase 2** | POC básica - um servidor com banco local |
| **Fase 3** | Desacoplamento - separa banco (RDS) da aplicação |
| **Fase 4** | Alta disponibilidade - ALB + Auto Scaling |

---

## 💰 Custo Estimado

**~$60/mês** incluindo:
- 2 servidores EC2: $17
- Load Balancer: $22
- Banco de dados RDS: $15
- Outros (storage, secrets): $6

---

## 🔄 Fluxo de Funcionamento

1. Usuário acessa a URL do ALB
2. ALB escolhe um servidor EC2 saudável
3. EC2 busca credenciais no Secrets Manager
4. EC2 consulta/atualiza dados no RDS
5. Resposta volta para o usuário

---

## ✅ Requisitos Atendidos

| Requisito | Como foi resolvido |
|-----------|-------------------|
| Funcional | App Node.js com CRUD completo |
| Balanceado | ALB distribui tráfego |
| Escalável | Auto Scaling (2-6 instâncias) |
| Disponível | Multi-AZ (2 zonas de disponibilidade) |
| Seguro | Subnets privadas, Security Groups, Secrets Manager |
| Econômico | Instâncias t3.micro (free tier elegível) |

---

## 🛠️ Tecnologias Utilizadas

### Backend
- **Node.js** - Runtime JavaScript
- **Express.js** - Framework web
- **mysql2** - Driver MySQL para Node.js
- **AWS SDK** - Conexão com serviços AWS

### Banco de Dados
- **MySQL 8.0** - Sistema de gerenciamento de banco de dados relacional

### Infraestrutura AWS
- VPC, Subnets, Internet Gateway
- EC2, RDS, ALB
- Auto Scaling Group
- Security Groups
- Secrets Manager
- IAM, Cloud9

---

## 📝 Aplicação Web

A aplicação é um sistema CRUD (Create, Read, Update, Delete) de registros de alunos:

### Funcionalidades
- **Listar** todos os alunos
- **Adicionar** novo aluno (nome, email, curso, ano)
- **Deletar** aluno existente
- **Atualizar** dados do aluno (via API)

### Endpoints da API
| Método | Rota | Descrição |
|--------|------|-----------|
| GET | `/` | Página principal (interface web) |
| GET | `/api/students` | Lista todos os alunos |
| POST | `/api/students` | Adiciona novo aluno |
| PUT | `/api/students/:id` | Atualiza aluno |
| DELETE | `/api/students/:id` | Remove aluno |
| GET | `/health` | Health check para o ALB |

---

Este é um padrão de arquitetura muito comum em produção para aplicações web empresariais na AWS!
