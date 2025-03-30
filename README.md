# Pipeline Cloud Build + Terraform

## **Objetivo**
Este projeto demonstra como configurar um pipeline de CI/CD utilizando **Google Cloud Build** e **Terraform** para provisionamento de infraestrutura como código (IaC). O objetivo é automatizar o processo de deployment e provisionamento de recursos no Google Cloud.


## **Descrição do Projeto**
O pipeline de CI/CD cria uma integração entre o **Google Cloud Build** e o **Terraform**, proporcionando um fluxo contínuo e automatizado de provisionamento de infraestrutura. O uso de **Cloud Source Repositories**, **Cloud Build Triggers**, e **Google Cloud Storage** garante que o processo seja escalável e eficiente.


## **Passo a Passo do Desenvolvimento**

### **Passo 1: Clonar o Repositório**
Abra o **Cloud Shell** e clone o repositório `terraform-exemplo2` com o seguinte comando:

```bash
git clone https://github.com/digitalinnovationone/terraform-gcp.git
cd terraform-gcp/terraform-exemplo2/
```

<p align="center">
  <img src="/images/fig_1.png?raw=true" alt="Git clone" width="80%" />
</p>


### **Passo 2: Criar Repositório no Cloud Source Repositories**
Crie um novo repositório no **Cloud Source Repositories** para armazenar o código.

<p align="center">
  <img src="/images/fig_2.png?raw=true" alt="Cloud Source Repositories" width="80%" />
</p>


### **Passo 3: Realizar Push para o Repositório**
Faça o push dos arquivos do diretório `terraform-exemplo2` para o repositório que você acabou de criar.

<p align="center">
  <img src="/images/fig_4.png?raw=true" alt="Push para Repositório" width="80%" />
</p>

<p align="center">
  <img src="/images/fig_3.png?raw=true" alt="Push para Repositório" width="80%" />
</p>


### **Passo 4: Criar Trigger no Cloud Build**
Acesse o **Cloud Build** e crie um **Trigger** que será responsável por automatizar o processo de build a partir do repositório criado.

<p align="center">
  <img src="/images/gif_1.gif?raw=true" alt="Cloud Build Trigger" width="80%" />
</p>


### **Passo 5: Criar Bucket no Google Cloud Storage**
Crie um bucket no **Google Cloud Storage** para armazenar o estado do Terraform (backend remoto).

<p align="center">
  <img src="/images/gif_2.gif?raw=true" alt="Google Cloud Storage" width="80%" />
</p>


### **Passo 6: Editar o Arquivo `main.tf`**
Edite o arquivo `main.tf` com as configurações do Terraform. Este arquivo define a infraestrutura a ser provisionada, incluindo a configuração do backend no Google Cloud Storage.

```hcl
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
    access_config {}
  }
}
```


### **Passo 7: Commit e Push para o Repositório**
Após editar o arquivo `main.tf`, salve as alterações, realize o commit e faça o push para o repositório.

#### **Resultado no Cloud Build**
O pipeline será acionado automaticamente e você verá o progresso no **Cloud Build**:

<p align="center">
  <img src="/images/fig_5.png?raw=true" alt="Cloud Build Resultado" width="80%" />
</p>


#### **Resultado no Google Cloud Storage**
O estado do Terraform será armazenado no bucket criado:

<p align="center">
  <img src="/images/fig_6.png?raw=true" alt="Google Cloud Storage Resultado" width="80%" />
</p>


#### **Instância Criada no Google Cloud**
O Terraform criará a instância de máquina virtual conforme a configuração definida.

<p align="center">
  <img src="/images/fig_7.png?raw=true" alt="Instância Criada" width="80%" />
</p>


## **Observações Importantes**
- Foi necessário conceder permissões de **Compute Network Admin** à conta de serviço do **Cloud Build** para que fosse possível criar a VPC.
- Este pipeline pode ser estendido para incluir outros recursos do Google Cloud, como **BigQuery**, **Google Kubernetes Engine (GKE)**, e **Cloud SQL**.


## **Conclusão**
Este pipeline proporciona uma solução robusta para provisionamento de infraestrutura como código no Google Cloud, usando **Terraform** e **Cloud Build** para automatizar o processo de deployment. Essa integração garante consistência e agilidade, permitindo que equipes de desenvolvimento e operações (DevOps) trabalhem de forma mais eficiente.
