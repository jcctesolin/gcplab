## task 01

### criar diretorio e arquivos

```
mkdir tflab
cd tflab
touch main.tf
touch variables.tf
mkdir modules
cd modules
mkdir instances
cd instances
touch instances.tf
touch outputs.tf
touch variables.tf
cd ..
mkdir storage
cd storage
touch storage.tf
touch outputs.tf
touch variables.tf
cd
```

### criar variaveis em todos os blocos
```
variable "region" {
 default = "<FILL IN REGION>"
}

variable "zone" {
 default = "<FILL IN ZONE>"
}

variable "project_id" {
 default = "<FILL IN PROJECT ID>"
}

```

### adicionar provider ao main.tf
```
terraform {
  required_providers {
    google = {
      source = "hashicorp/google"
      version = "6.3.0"
    }
  }
}

provider "google" {
  project     = var.project_id
  region      = var.region
  zone        = var.zone
}


module "instances" {
  source     = "./modules/instances"
}
```



### verificar versão Terraform
``terraform version``

### inicializar Terraform

``terraform init``

## task 02

### editar *instances.tf*

```
resource "google_compute_instance" "tf-instance-1" {
  name         = "tf-instance-1"
  machine_type = "<FILL IN MACHINE TYPE>"

  boot_disk {
    initialize_params {
      image = "<FILL IN BOOT DISK>"
    }
  }

  network_interface {
 network = "<FILL IN NETWORK>"
  }

metadata_startup_script = <<-EOT
#!/bin/bash
EOT

allow_stopping_for_update = true
}

resource "google_compute_instance" "tf-instance-2" {
  name         = "tf-instance-2"
  machine_type = "<FILL IN MACHINE TYPE>"

  boot_disk {
    initialize_params {
      image = "<FILL IN BOOT DISK>"
    }
  }

  network_interface {
 network = "<FILL IN NETWORK>"
  }

metadata_startup_script = <<-EOT
#!/bin/bash
EOT

allow_stopping_for_update = true
}
```

### importar VMs

``terraform import module.instances.google_compute_instance.tf-instance-1 <FILL IN INSTANCE 1 ID>``

``terraform import module.instances.google_compute_instance.tf-instance-2 <FILL IN INSTANCE 2 ID>``

### aplicar modificações

``terraform plan``

``terraform apply``

## task 03

### editar *storage.tf*

```
resource "google_storage_bucket" "storage-bucket" {
  name          = "<FILL IN BUCKET NAME>"
  location      = "US"
  force_destroy = true
  uniform_bucket_level_access = true
}
```

### editar *main.tf*

```
module "storage" {
  source     = "./modules/storage"
}
```

### inicializar modulo

``terraform init``

``terraform apply``

### bucket como remote backend

```
terraform {
  backend "gcs" {
    bucket  = "<FILL IN BUCKET ID>"
 prefix  = "terraform/state"
  }
  required_providers {
    google = {
      source = "hashicorp/google"
      version = "3.55.0"
    }
  }
}
```
### incialiar backend
``terraform init``

## task 04

### atualizar vms 

```
resource "google_compute_instance" "tf-instance-1" {
  name         = "tf-instance-1"
  machine_type = "n1-standard-2"
  zone         = "us-central1-a"
  allow_stopping_for_update = true

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-10"
    }
  }

  network_interface {
 network = "default"
  }
}

resource "google_compute_instance" "tf-instance-2" {
  name         = "tf-instance-2"
  machine_type = "n1-standard-2"
  zone         = "us-central1-a"
  allow_stopping_for_update = true

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-10"
    }
  }

  network_interface {
 network = "default"
  }
}

resource "google_compute_instance" "tf-instance-471462" {
  name         = "tf-instance-471462"
  machine_type = "e2-standard-2"
  zone         = "us-central1-a"
  allow_stopping_for_update = true

  boot_disk {
    initialize_params {
      image = "debian-11-bullseye-v20240910"
    }
  }

  network_interface {
 network = "default"
  }
}
```

### aplicar mudanças

``terraform init``

``terraform apply``

## task 05

### marcar vm3

```
terraform taint module.instances.google_compute_instance.<FILL IN INSTANCE 3 NAME>
```

### remover cfg da vm3

remover linhas do arquivo *instances.tf*

### aplicar mudanças

``terraform init``

``terraform apply``

## task 06

### importar Network module do Registry no *main.tf*

```
module "vpc" {
    source  = "terraform-google-modules/network/google"
    version = "6.0.0"

    project_id   = "<FILL IN PROJECT ID>"
    network_name = "<FILL IN NETWORK NAME>"
    routing_mode = "GLOBAL"

    subnets = [
        {
            subnet_name           = "subnet-01"
            subnet_ip             = "10.10.10.0/24"
            subnet_region         = "us-central1"
        },
        {
            subnet_name           = "subnet-02"
            subnet_ip             = "10.10.20.0/24"
            subnet_region         = "us-central1"
        }
    ]
}
```
### inicializar VPC

``terraform init``

``terraform apply``

### atualizar o nome da rede no arquivo instances.tf

- tf-instance 1

  network_interface {
 network = "<FILL IN NETWORK NAME>"
    subnetwork = "subnet-01"
  }

- tf-instance-2

    network_interface {
 network = "<FILL IN NETWORK NAME>"
    subnetwork = "subnet-02"
  }

### aplicar mudanças

``terraform init``

``terraform apply``

## task 07

### adicionar o firewall ao *main.tf*

```
resource "google_compute_firewall" "tf-firewall" {
  name    = "tf-firewall"
 network = "projects/<FILL IN PROJECT_ID>/global/networks/<FILL IN NETWORK NAME>"

  allow {
    protocol = "tcp"
    ports    = ["80"]
  }

  source_tags = ["web"]
  source_ranges = ["0.0.0.0/0"]
}
```
### aplicar alterações

``terraform init``

``terraform apply``
