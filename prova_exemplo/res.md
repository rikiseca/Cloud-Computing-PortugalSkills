# Guia de Resolução - Projeto Cloud Computing AWS

## Índice

1. [Configuração Inicial](#configuração-inicial)
2. [Criação das VPCs](#criação-das-vpcs)
3. [Configuração do Transit Gateway](#configuração-do-transit-gateway)
4. [Configuração S3](#configuração-s3)
5. [Configuração RDS](#configuração-rds)
6. [Auto Scaling Group](#auto-scaling-group)
7. [Configuração dos Servidores](#configuração-dos-servidores)
8. [DNS e Load Balancers](#dns-e-load-balancers)

## Configuração Inicial

### Pré-requisitos

- Conta AWS com acesso apropriado
- AWS CLI instalado
- Chave SSH fornecida
- Conhecimento básico de networking

### Recursos úteis

- [AWS CLI Installation Guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- [AWS Regions and Availability Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html)

## Criação das VPCs

### VPC em US-EAST-1 (pdl-vpc)

```bash
aws ec2 create-vpc --cidr-block 10.0.0.0/20 --region us-east-1 --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=pdl-vpc}]'
```

### VPC em US-EAST-1 (web-vpc)

```bash
aws ec2 create-vpc --cidr-block 10.0.16.0/20 --region us-east-1 --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=web-vpc}]'
```

### VPC em US-WEST-2 (angra-vpc)

```bash
aws ec2 create-vpc --cidr-block 172.16.0.0/16 --region us-west-2 --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=angra-vpc}]'
```

### Recursos úteis - VPCs

- [VPC Creation Guide](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html)
- [Subnet Calculator](https://www.subnet-calculator.com/)

## Configuração do Transit Gateway

1. Criar Transit Gateway

```bash
aws ec2 create-transit-gateway --description "TG for Schools" --region us-east-1
```

2. Anexar VPCs ao Transit Gateway
3. Configurar rotas entre pdl-vpc e angra-vpc

### Recursos úteis - Transit Gateway

- [Transit Gateway Guide](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-getting-started.html)

## Configuração S3

1. Criar bucket

```bash
aws s3api create-bucket --bucket PrimeiroUltimoNomeXXXXX --region us-east-1
```

2. Configurar notificações por email (SNS)

### Recursos úteis - S3

- [S3 Event Notifications](https://docs.aws.amazon.com/AmazonS3/latest/userguide/NotificationHowTo.html)

## Configuração RDS

1. Criar instância MySQL

```bash
aws rds create-db-instance \
    --db-instance-identifier Northwind \
    --db-instance-class db.t3.micro \
    --engine mysql \
    --master-username root \
    --master-user-password Passw0rd \
    --publicly-accessible
```

2. Importar base de dados Northwind

### Recursos úteis - RDS

- [RDS MySQL Guide](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_MySQL.html)

## Auto Scaling Group

1. Criar Launch Template
2. Configurar EFS
3. Criar Target Group
4. Criar Network Load Balancer
5. Configurar Auto Scaling Group com regras de CPU

### Configuração do stress-ng

```bash
git clone https://github.com/ColinIanKing/stress-ng.git
cd stress-ng
make
sudo make install
```

### Recursos úteis - Auto Scaling Group

- [Auto Scaling Guide](https://docs.aws.amazon.com/autoscaling/ec2/userguide/get-started-with-ec2-auto-scaling.html)
- [EFS Setup Guide](https://docs.aws.amazon.com/efs/latest/ug/getting-started.html)

## Configuração dos Servidores

### srv.pdl.local

1. Lançar instância t2.small com Ubuntu
2. Configurar interfaces de rede
3. Configurar NAT
4. Instalar servidor de certificados

### dmzwin.pdl.local

1. Lançar instância t2.small com Windows Server
2. Instalar IIS, PHP
3. Configurar páginas web

[Continua com configurações detalhadas para cada servidor...]

### Recursos úteis - EC2

- [EC2 Windows Guide](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/EC2_GetStarted.html)
- [EC2 Linux Guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html)
- [IIS Installation](https://docs.microsoft.com/en-us/iis/get-started/whats-new-in-iis-10/new-features-introduced-in-iis-10)

## DNS e Load Balancers

1. Configurar Route 53 para zonas DNS
2. Criar registos para <www.enta.pt> e <www.regional.pt>
3. Configurar Network Load Balancers
4. Configurar certificados SSL

### Recursos úteis - DNS

- [Route 53 Guide](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/getting-started.html)
- [NLB Guide](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html)

## Verificação Final

Checklist de verificação:

- [ ] Todas as VPCs criadas e configuradas
- [ ] Transit Gateway funcionando
- [ ] S3 com notificações
- [ ] RDS com Northwind importado
- [ ] Auto Scaling Group respondendo a CPU
- [ ] Todos os servidores acessíveis
- [ ] DNS resolvendo corretamente
- [ ] Load Balancers funcionando
- [ ] FTP sincronizando arquivos
- [ ] Certificados SSL instalados

### Recursos úteis - AWS Docs

- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [AWS Documentation](https://docs.aws.amazon.com/)
