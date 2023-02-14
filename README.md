# Pipeline Cloud Build + Terraform
---

## Objetivo
Cria um pipeline de CI/CD com Google Cloud Build e Terraform para provisionamento de Infraestrutura como código. Criando Pipeline de CI/CD com Cloud Build e Terraform.

## Desenvolvimento
#### Passo 1:

Abrir o cloud shell e clonar o repositório terraform-exemplo2

```shell
https://github.com/digitalinnovationone/terraform-gcp.git

cd terraform-gcp/terraform-exemplo2/
```

<p>
 <img src="/images/fig_1.png?raw=true" alt="git clone" width="80%" height="50%" />
</p>

#### Passo 2:

Cria um repositório no Cloud Source Repositories.

<p>
 <img src="/images/fig_2.png?raw=true" alt="Cloud source repositories" width="80%" height="50%" />
</p>

#### Passo 3:

Faz um push dos arquivos do diretório terraform exemplo 2 para o repositório.

<p>
 <img src="/images/fig_4.png?raw=true" alt="Cloud source repositories" width="80%" height="50%" />
</p>

<p>
 <img src="/images/fig_3.png?raw=true" alt="Cloud source repositories" width="80%" height="50%" />
</p>


#### Passo 4:

Acessa o Cloud Build e cria um Triggers com o repositório criado.

<p>
 <img src="/images/gif_1.gif?raw=true" alt="Cloud Build" width="80%" height="50%" />
</p>

#### Passo 5:

Acessa o Google Storage e cria um Bucket para salvar os estados do Terraform.

<p>
 <img src="/images/gif_2.gif?raw=true" alt="Cloud Storage" width="80%" height="50%" />
</p>

#### Passo 6:

Editar o arquivo main.tf com as especificações criadas.

```
terraform {
  required_providers {
    google = {
      source = "hashicorp/google"
    }
  }

   backend "gcs" {
    bucket  = "cloudbuildterraform-bucket"
    prefix  = "terraform/state"
  }

}

provider "google" {
  project = "jacivaldocarvalho-devops-iac"
  region  = "us-central1"
  zone    = "us-central1-c"
}

resource "google_compute_network" "vpc_network" {
  name = "${var.network_name}"
}

resource "google_compute_instance" "vm_instance" {
  name         = "terraform-instance"
  machine_type = "f1-micro"
  tags = ["prod"]

  labels = {
    centro_custo = "${var.centro_custo_rh}"
  }

  boot_disk {
    initialize_params {
        image = "debian-cloud/debian-11"
    }
  }
  network_interface {
    network = google_compute_network.vpc_network.name
    access_config {
    }
  }
}
```

#### Passo 7:

Salva as alterações, realiza o commit, e faz o push para o repositório. Resultados abaixo:

##### Cloud Build
<p>
 <img src="/images/fig_5.png?raw=true" alt="Cloud Build" width="80%" height="50%" />
</p>


##### State criado no buket
<p>
 <img src="/images/fig_6.png?raw=true" alt="Cloud Bucket" width="80%" height="50%" />
</p>

##### VM criada

##### State criado no buket
<p>
 <img src="/images/fig_7.png?raw=true" alt="Cloud Instance" width="80%" height="50%" />
</p>

OBS. Foi necessário dar permissão de Compute Network Admin a conta de serviço do build para criar a vpc.