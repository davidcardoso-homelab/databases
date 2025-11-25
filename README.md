# databases

Repositório para provisionamento e gerenciamento de bancos de dados no cluster Kubernetes via ArgoCD.
Este projeto contém manifests e valores para provisionar recursos como Namespaces, PersistentVolumes, Secrets e aplicações (ex.: Postgres) de forma GitOps.

## Estrutura
- `setup/` — Manifests de bootstrap do projeto (AppProject do ArgoCD, `repository` com SSH se necessário).
  - Manifests principais: [`setup/project.yaml`](setup/project.yaml), [`setup/repository.yaml`](setup/repository.yaml)
- `postgres/` — Manifests específicos do Postgres (deployments/statefulsets, services, PVCs, secret de credenciais).
  - Manifests de deploy: [`postgres/setup/application.yaml`](postgres/setup/application.yaml)  
  - Secrets e PVs: [`postgres/setup/secret.yaml`](postgres/setup/secret.yaml), [`postgres/setup/persistent-volume.yaml`](postgres/setup/persistent-volume.yaml)
  - Dashboards/monitoring: [`postgres/monitoring/`](postgres/monitoring/)
- `postgres/values/` — Exemplos e valores reutilizáveis: [`postgres/values/values.yaml`](postgres/values/values.yaml)

## Pré-requisitos
- Cluster Kubernetes funcional.
- ArgoCD instalado e configurado (opcional: aplicar manifests com `kubectl`).
- `kubectl` com contexto apontando para o cluster.
- Permissão para criar Namespaces, PersistentVolumes e Secrets.

## Configurações importantes
- Configure a chave SSH privada em [`setup/repository.yaml`](setup/repository.yaml) (`sshPrivateKey`) se o ArgoCD acessar repositórios privados.
- Configure as credenciais do Postgres em [`postgres/setup/secret.yaml`](postgres/setup/secret.yaml) antes de aplicar (não comite senhas em repositórios públicos).
- Ajuste caminhos e parâmetros de [`postgres/setup/persistent-volume.yaml`](postgres/setup/persistent-volume.yaml) e [`postgres/values/values.yaml`](postgres/values/values.yaml) conforme sua infraestrutura de storage.

## Deploy (exemplo rápido)
1. Crie o namespace:
```sh
kubectl create namespace databases
```

2. Aplique os manifests de setup (provisiona AppProject/credentials):
```sh
kubectl apply -f setup/
```

3. Aplique os manifests do Postgres (ou deixe o ArgoCD sincronizar):
```sh
kubectl apply -f postgres/setup/
```

4. Verifique os recursos:
```sh
kubectl get pods,svc,pvc -n databases
```

## Observações
- Não comite chaves privadas ou segredos em repositórios públicos — use `SealedSecrets`, `ExternalSecrets` ou mantenha `repository.yaml` fora do repositório público.
- Para ambientes locais com MetalLB, garanta que o pool de IPs esteja configurado (ex.: `metallb/` em outro workspace).
- Configure backup e política de retenção para os volumes que armazenam dados do banco.

## Referências locais
- Manifests de setup: [`setup/project.yaml`](setup/project.yaml), [`setup/repository.yaml`](setup/repository.yaml)  
- Manifests do Postgres: [`postgres/`](postgres/)

---
Projeto mantido por [davidcardoso-homelab](https://github.com/davidcardoso-homelab).