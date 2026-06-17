# DevOps-project-419534

Projekt przedstawia automatyczny pipeline CI/CD dla aplikacji kontenerowej wdrażanej do Azure Kubernetes Service.

## Jak działa projekt?

Po każdym wysłaniu zmian do brancha `main` GitHub Actions:

1. uruchamia testy jednostkowe,
2. buduje obraz Docker,
3. uruchamia kontener i sprawdza endpoint `/health`,
4. loguje się do Azure przez OIDC,
5. wysyła obraz do Azure Container Registry,
6. aktualizuje Deployment w Azure Kubernetes Service,
7. czeka na poprawne zakończenie rolloutu.

```text
GitHub → GitHub Actions → ACR → AKS → LoadBalancer → użytkownik
```

## Wykorzystane technologie

* GitHub Actions — pipeline CI/CD,
* Docker — konteneryzacja aplikacji,
* Terraform — infrastruktura jako kod,
* Azure Container Registry — przechowywanie obrazów,
* Azure Kubernetes Service — uruchamianie aplikacji,
* Kubernetes — Deployment, pody i Service,
* OIDC — logowanie GitHub Actions do Azure bez stałego hasła.

## Infrastruktura Terraform

Terraform tworzy:

* Resource Group `rg-devops-419534`,
* ACR `acr419534devops2026`,
* AKS `aks-devops-419534`,
* rolę `AcrPull`, pozwalającą AKS pobierać obrazy z ACR.

## Kubernetes

Manifest tworzy:

* Deployment `app`,
* 2 repliki aplikacji,
* readiness probe i liveness probe,
* Service typu `LoadBalancer`,
* przekierowanie portu 80 do portu kontenera 8080.

## Endpointy

* `/` — informacje o projekcie,
* `/health` — sprawdzenie działania aplikacji,
* `/demo` — endpoint dodany w celu pokazania automatycznego wdrożenia.

## Aktualizacja aplikacji

Obraz jest oznaczany tagiem odpowiadającym SHA commita:

```text
acr419534devops2026.azurecr.io/app:<git-sha>
```

Po zmianie kodu wystarczy wykonać commit. Pipeline automatycznie buduje nowy obraz i aktualizuje Deployment w AKS.

