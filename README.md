# shopcar-database

O repositório `shopcar-database` é responsável por manter os manifestos Kubernetes e os scripts de provisionamento dos bancos de dados utilizados na plataforma ShopCar. Ele garante a criação de instâncias persistentes de PostgreSQL e MongoDB dentro do cluster EKS da AWS, além da inicialização de esquemas específicos.

## O que este repositório provisiona?

- **PostgreSQL** com `StatefulSet`, `Service`, `PVC`, `ConfigMap`, `Secret`
- **MongoDB** com `Deployment`, `Service`, `PVC`, `ConfigMap`, `Secret`
- Criação automática dos schemas via batch do kubernetes:
    - `ordering` para o microserviço `ms-order`
    - `vehicle` ou `eModulo` para o microserviço `ms-vehicle`

## Estrutura do Projeto

```
shopcar-database/
├── postgres/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── pvc.yaml
│   ├── vc.yaml
│   ├── configmap.yaml
│   ├── secret.yaml
│   └── batch.yaml
├── mongodb/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── pvc.yaml
│   ├── vc.yaml
│   ├── configmap.yaml
│   └── secret.yaml
```

## Kubernetes

Todos os serviços de banco de dados são criados com persistência e prontos para produção em cluster EKS:

- **PostgreSQL** é criado com `Deployment` simples e volume persistente
- **MongoDB** é criado com `Deployment` simples e volume persistente
- Ambos expostos via `ClusterIP` para uso interno

## Schemas Criados

Scripts localizados em `postgres/batch.yaml` são executados após alguns minutos:

### `create-ordering-schema.sql`
```sql
CREATE SCHEMA IF NOT EXISTS ordering;
```

### `create-vehicle-schema.sql`
```sql
CREATE SCHEMA IF NOT EXISTS vehicle;
```

> Os serviços `ms-order` e `ms-vehicle` utilizam esses schemas em suas configurações.

## Deploy

Para aplicar os bancos no cluster Kubernetes:

```bash
kubectl apply -f postgres/
kubectl apply -f mongodb/
```

> Recomendado executar após criação do cluster via `shopcar-aws-infra`

## Segurança

- Usuários e senhas de acesso são configurados via `Secret`
- PVCs garantem persistência mesmo após reinicializações

---

## Contribuidores

- Kreverson Silva – DBA Cloud & DevOps