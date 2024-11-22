# Terraform

# providers block
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "=3.0.0"
    }
  }
}
#Configure the Microsoft Azure Provider
provider "azurerm" {
 
features {}
subscription_id   = "d34f632b-eec7-424e-b01f-db1282c04f35"
  client_id       = "4fe994cd-5a72-4719-ab2b-38bece736e68"
  client_secret   = "Zz~8Q~Dk-DqLrOnY5pwnozjeO64Ri2IDXJm96a~-"
  tenant_id       = "b8835ac2-9a80-419b-8544-da97c53a3098"
}
#backend
terraform {
     backend "azurerm" {
     resource_group_name  = "TestTerraform"  
     storage_account_name = "mahiterraformtest"                      
     container_name       = "statefiles" 
     key                  = "test.terraform.tfstate"
     access_key           = "au+bu/10pLCJENTb3qvFoAxEmJ/Os9cs0DTXD1NpvjJSqJWxcJZeaBfLBBqon3fNX3Qcn3kUrzdt+ASt5m0bgA=="   
  }     
 }
  
# Create a resource group

 resource "azurerm_storage_account" "st" {
  name                     = "mahiterraformtest"
  resource_group_name      = azurerm_resource_group.rg.name
  location                 = azurerm_resource_group.rg.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "cont" {
  name                  = "statefiles"
  storage_account_name  = azurerm_storage_account.st.name
  container_access_type = "private"
 }



resource "azurerm_resource_group" "rg" {
  name     = "TestTerraform"
  location = "West US 2"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "vnet1"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_subnet" "sn" {
  name                 = "subnet1"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.2.0/24"]
}

resource "azurerm_network_interface" "nic" {
  name                = "nic01"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  ip_configuration {
    name                          = "ipconfiguration1"
    subnet_id                     = azurerm_subnet.sn.id
    private_ip_address_allocation = "Dynamic"
  }
}
