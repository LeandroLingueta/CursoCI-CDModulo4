# Pipeline CI/CD com GitHub Actions

## Objetivo

Este projeto demonstra a implementação de uma pipeline CI/CD utilizando GitHub Actions.

O fluxo possui três etapas:

1. Continuous Integration (CI)
2. Deploy Staging
3. Deploy Production

---

## Funcionamento do Pipeline

Sempre que ocorre um push na branch `main`, o workflow é executado.

Fluxo:

CI → Deploy Staging → Deploy Production

A dependência entre os jobs é controlada pela propriedade `needs`.

### CI

O job `ci`:

* realiza checkout do código;
* instala dependências;
* executa os testes automatizados com pytest.

Caso algum teste falhe, os próximos jobs não serão executados.

### Deploy Staging

O job `deploy-staging` possui:

```yaml
needs: ci
environment: staging
```

Isso significa que ele somente executa após a conclusão bem-sucedida do CI.

### Deploy Production

O job `deploy-production` possui:

```yaml
needs: deploy-staging
environment: production
```

Isso significa que ele somente executa após a conclusão bem-sucedida do deploy em staging.

---

## Papel de Cada Ambiente

### Staging

Ambiente utilizado para validação da aplicação antes da publicação em produção.

Permite verificar se o deploy foi realizado corretamente sem impactar usuários finais.

### Production

Ambiente utilizado pelos usuários finais.

Somente versões aprovadas devem ser implantadas neste ambiente.

---

## Aprovação Manual

A aprovação manual ocorre no ambiente `production`.

Para configurar:

1. Acesse o repositório no GitHub.
2. Vá em Settings.
3. Clique em Environments.
4. Selecione o ambiente `production`.
5. Configure a opção "Required reviewers".

Quando o workflow chegar ao job de produção, o GitHub interromperá a execução e solicitará aprovação de um revisor autorizado.

Somente após a aprovação o deploy será executado.

---

## Utilização de Secrets

O workflow utiliza secrets para armazenar informações sensíveis.

Exemplos:

* STAGING_API_KEY
* PRODUCTION_API_KEY

Os valores são cadastrados em:

Settings → Secrets and variables → Actions

Exemplo de uso:

```yaml
env:
  API_KEY: ${{ secrets.PRODUCTION_API_KEY }}
```

Dessa forma o valor não fica exposto no código-fonte.

---

## Como Visualizar a Execução

1. Acesse o repositório no GitHub.
2. Clique na aba Actions.
3. Selecione o workflow "Continuous Deployment".
4. Escolha uma execução.
5. Visualize os logs de cada job:

* Continuous Integration
* Deploy Staging
* Deploy Production

Também é possível verificar o histórico de execuções e identificar falhas em qualquer etapa do pipeline.
