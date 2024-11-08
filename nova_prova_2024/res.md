# Guia Detalhado para Implementação AWS - ENTA e EPPV

## Índice

- [Guia Detalhado para Implementação AWS - ENTA e EPPV](#guia-detalhado-para-implementação-aws---enta-e-eppv)
  - [Índice](#índice)
  - [Configuração Inicial](#configuração-inicial)
    - [Preparação do Ambiente](#preparação-do-ambiente)
  - [Configuração das VPCs](#configuração-das-vpcs)
    - [PDL-VPC (US-EAST-1)](#pdl-vpc-us-east-1)
    - [WEB-VPC (US-EAST-1)](#web-vpc-us-east-1)
    - [ANGRA-VPC (US-WEST-2)](#angra-vpc-us-west-2)
    - [Interconexão das VPCs](#interconexão-das-vpcs)
  - [Configuração do S3](#configuração-do-s3)
  - [Configuração do RDS MySQL](#configuração-do-rds-mysql)
  - [Auto Scaling Group](#auto-scaling-group)
  - [Configuração US-EAST-1](#configuração-us-east-1)
    - [srv.pdl.local](#srvpdllocal)
    - [cli.pdl.local](#clipdllocal)
    - [dmzwin.pdl.local e dmzlux.pdl.local](#dmzwinpdllocal-e-dmzluxpdllocal)
  - [Configuração US-WEST-2](#configuração-us-west-2)
    - [srv.angra.local](#srvangralocal)
    - [cli.angra.local](#cliangralocal)
    - [intrawin.angra.local e intralux.angra.local](#intrawinangralocal-e-intraluxangralocal)
  - [Configuração de DNS e Certificados](#configuração-de-dns-e-certificados)
    - [Pontos de Verificação Final](#pontos-de-verificação-final)

## Configuração Inicial

### Preparação do Ambiente

1. Criar Key Pairs em ambas as regiões (us-east-1 e us-west-2)
2. Confirmar acesso às AMIs necessárias no Quick Start
3. Preparar ambiente local com as ferramentas necessárias:
   - MySQL Workbench
   - FileZilla
   - Navegadores web
   - Ferramentas de monitorização

## Configuração das VPCs

### PDL-VPC (US-EAST-1)

1. Criar VPC com as especificações:

   ```
   Name: pdl-vpc
   CIDR: 10.0.0.0/20
   1 AZ, 1 subnet pública, 2 subnets privadas
   ```

2. Configurar S3 Gateway Endpoint
3. Habilitar DNS hostnames e resolution

### WEB-VPC (US-EAST-1)

1. Criar VPC com as especificações:

   ```
   Name: web-vpc
   CIDR: 10.0.16.0/20
   3 AZs, 3 subnets públicas
   ```

2. Configurar S3 Gateway Endpoint
3. Habilitar DNS host names e resolution

### ANGRA-VPC (US-WEST-2)

1. Criar VPC com as especificações:

   ```
   Name: angra-vpc
   CIDR: 172.16.0.0/16
   1 AZ, 1 subnet pública, 2 subnets privadas
   IPv6 habilitado
   ```

2. Configurar S3 Gateway Endpoint e Egress-only Internet Gateway
3. Habilitar DNS hostnames e resolution

### Interconexão das VPCs

1. Configurar Transit Gateway ou VPC Peering (conforme especificado no dia)
2. Configurar rotas entre PRIVATE-NET-1 de ENTA e EPPV
3. Verificar que não existem rotas default nas tabelas de roteamento das primeiras redes

## Configuração do S3

1. Criar bucket em us-east-1:

   ```
   Nome: primeiroultimoNomeXXXXX
   ```

2. Configurar notificações:
   - Criar tópico SNS
   - Configurar endpoint de email (<medeiros@enta.pt>)
   - Configurar evento de upload
3. Upload das chaves privadas:
   - Chave us-east-1
   - Chave us-west-2

## Configuração do RDS MySQL

1. Criar instância RDS em us-east-1:

   ```
   DB instance identifier: Northwind
   Engine: MySQL
   Master username: root
   Password: Passw0rd
   Public access: Yes
   ```

2. Importar base de dados Northwind
3. Configurar security groups apropriados
4. Testar conexão via MySQL Workbench de ambos os clientes

## Auto Scaling Group

1. Criar EFS para conteúdo web compartilhado
2. Preparar Launch Template:
   - AMI: Amazon Linux 2023
   - Instance Type: M5.large
   - Detailed monitoring enabled
   - User data para instalação de dependências:
     - Apache/Nginx
     - PHP
     - stress-ng
     - Configuração SSL
3. Criar Target Group com health checks
4. Configurar Network Load Balancer
5. Criar Auto Scaling Group:
   - Min: 1, Desired: 1, Max: 3
   - Scaling policies:

     ```
     CPUUtilization < 50: 1 instância
     50 <= CPUUtilization < 75: 2 instâncias
     75 <= CPUUtilization: 3 instâncias
     ```

   - Warm up: 60 segundos
   - Cooldown: 60 segundos

## Configuração US-EAST-1

### srv.pdl.local

1. Lançar instância t3.small com Ubuntu/Debian
2. Configurar Elastic IP
3. Configurar NAT e routing
4. Instalar e configurar servidor de certificados
5. Configurar security groups para acesso SSH/RDP

### cli.pdl.local

1. Lançar instância M5.large com Ubuntu/Debian
2. Instalar software necessário:
   - Chromium
   - Wireshark
   - FileZilla
   - MySQL Workbench
3. Configurar acesso aos recursos web
4. Configurar conexões FTP
5. Verificar conectividade conforme especificações

### dmzwin.pdl.local e dmzlux.pdl.local

1. Configurar servidores web (IIS e Apache)
2. Configurar sincronização de conteúdo
3. Configurar servidores FTP
4. Configurar Network Load Balancers (público e privado)

## Configuração US-WEST-2

### srv.angra.local

1. Lançar instância t3.small com Linux
2. Configurar Elastic IP
3. Configurar Nginx com HTTP/HTTPS
4. Configurar NAT e routing

### cli.angra.local

1. Lançar instância M5.large com Windows Server 2022
2. Instalar software necessário
3. Configurar acesso aos recursos
4. Verificar conectividade

### intrawin.angra.local e intralux.angra.local

1. Configurar servidores web (IIS e Nginx)
2. Configurar sincronização de conteúdo
3. Configurar servidores FTP
4. Configurar Network Load Balancer privado

## Configuração de DNS e Certificados

1. Configurar Route 53 em ambas as regiões:
   - Registros A para todas as máquinas
   - Registros CNAME para Load Balancers
2. Configurar certificados SSL:
   - <www.enta.pt>
   - <www.eppv.pt>
   - <www.nacional.pt>
   - intranet.angra.local
3. Configurar redirecionamento HTTP para HTTPS onde necessário

### Pontos de Verificação Final

1. Testar conectividade entre todas as redes
2. Verificar funcionamento do Auto Scaling
3. Confirmar acesso aos recursos web
4. Verificar sincronização de conteúdo
5. Testar notificações S3
6. Verificar acesso ao RDS
7. Confirmar funcionamento dos certificados SSL
