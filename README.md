# Terraform & Packer Automation Script

Script de automatización para simplificar operaciones de Terraform y Packer en múltiples entornos con características avanzadas como modo interactivo y manejo robusto de errores.

## 🚀 Características

- **Multi-entorno**: Soporte para múltiples entornos (pre, pro, etc.)
- **Modo interactivo**: Selección visual de recursos específicos para aplicar cambios
- **Gestión automática de templates**: Procesamiento automático de archivos `.tpl`
- **Limpieza automática**: Gestión inteligente de archivos temporales
- **Validaciones robustas**: Verificación de dependencias y estructuras
- **Logging mejorado**: Sistema de logs con colores y niveles
- **Manejo de errores**: Traps para limpieza automática en interrupciones

## 📋 Requisitos

### Herramientas necesarias
- **Terraform** >= 1.0
- **Packer** (para builds de AMIs)
- **jq** (requerido solo para modo interactivo)
- **dos2unix** (opcional, para procesamiento de templates)

### Estructura de directorios esperada
```
proyecto/
├── make                    # Este script
├── templates/             # Templates .tpl (opcional)
│   └── *.tpl
├── pre/                   # Entorno pre-producción
│   ├── main.tf
│   └── terraform.tfvars
├── pro/                   # Entorno producción
│   ├── main.tf
│   └── terraform.tfvars
└── amis/                  # Templates Packer (opcional)
    ├── webapp/
    │   └── webapp.pkr.hcl
    └── api/
        └── api.pkr.hcl
```

## 🎯 Comandos Terraform

### Comandos básicos
```bash
# Inicializar entorno
./make init <environment>

# Generar plan
./make plan <environment>

# Aplicar cambios
./make apply <environment>
```

### Targeting específico
```bash
# Target específico
./make plan pre -target=module.alb.aws_lb.main
./make apply pro -target=aws_instance.web

# Modo interactivo (requiere jq)
./make plan pre -target
./make apply pro -target
```

### Argumentos adicionales
```bash
# Pasar argumentos adicionales a Terraform
./make plan pre -var="instance_type=t3.large" -parallelism=5
./make apply pro -auto-approve -compact-warnings
```

## 🏗️ Comandos Packer

### Builds de AMIs
```bash
# Build básico
./make build webapp
./make build api

# Build con variables personalizadas
./make build webapp -var="ami_name=custom-webapp-v2"
./make build api -var="instance_type=t3.medium" -var="region=eu-west-1"
```

## 🎨 Modo Interactivo

El modo interactivo permite seleccionar recursos específicos de forma visual:

1. **Activación**: Usar `-target` sin valor
   ```bash
   ./make plan pre -target
   ```

2. **Interfaz visual**: El script mostrará:
   - Lista numerada de recursos con cambios
   - Código de colores por tipo de acción:
     - 🟢 **Verde**: Recursos nuevos (create)
     - 🟡 **Amarillo**: Recursos modificados (update)  
     - 🔴 **Rojo**: Recursos eliminados (delete)

### Ejemplo de salida interactiva
```
Resources that will be modified
=================================
   1) aws_instance.web (create)
   2) aws_lb.main (update)
   3) aws_security_group.old (delete)

Choose Resource (1-3, 'a' for all, 'c' to Cancel): 1
```

## 🚨 Troubleshooting

### Errores comunes

**Error: jq no encontrado**
```bash
# Ubuntu/Debian
sudo apt-get install jq

# macOS
brew install jq

# CentOS/RHEL
sudo yum install jq
```

**Error: Entorno no encontrado**
- Verificar que el directorio del entorno existe
- Usar `ls -la` para ver entornos disponibles

**Error: Plan corrupto**
- Los archivos temporales se limpian automáticamente
- Reintentar la operación


**Nota**: Este script asume familiaridad con Terraform y Packer. Para usuarios nuevos, se recomienda revisar la documentación oficial de ambas herramientas.