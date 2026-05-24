# 🟣 Terraform Notes

---

## 📦 Variables

- Every variable used in `root/main.tf` **must** be declared in the module's `variables.tf`
- Module's `variables.tf` **can have more** variables than root passes — extras get defaults or are optional

---

## 🗂️ Modules

> A module is simply a **folder** containing a reusable set of resources.

### Core files inside every module
| File | Purpose |
|------|---------|
| `main.tf` | Resource definitions |
| `variables.tf` | Declares what the module accepts |
| `outputs.tf` | Exposes values to the caller |

### Folder Structure

```
terraform/
├── main.tf              # Calls modules & passes values
├── variables.tf         # Declares root variables
├── terraform.tfvars     # Provides actual values (ONLY HERE)
└── modules/
    ├── ec2/
    │   ├── main.tf      # EC2 resource definition
    │   ├── variables.tf # Declares what EC2 module accepts
    │   └── outputs.tf   # (NO terraform.tfvars inside modules!)
    ├── vpc/
    └── s3/
```

### Module Source Paths

| Path | Meaning |
|------|---------|
| `./modules/vpc` | Current dir → modules → vpc |
| `../shared/database` | Parent dir → shared → database |
| `/home/user/terraform` | Absolute path from root |
| `~/Documents/code` | Home dir → Documents → code |

---

## 💻 Commands

### Targeted Plan (dry-run — preview changes on specific resources only)

```bash
terraform plan \
  -target=aws_s3_bucket_public_access_block.my_specific_bucket \
  -target=aws_s3_bucket_acl.my_specific_bucket \
  -target=aws_s3_bucket_policy.my_specific_bucket
```

### Targeted Apply (apply changes to specific resources only)

```bash
terraform apply \
  -target=aws_s3_bucket_public_access_block.my_specific_bucket \
  -target=aws_s3_bucket_acl.my_specific_bucket \
  -target=aws_s3_bucket_policy.my_specific_bucket
```

> ✅ EC2s, VPCs, Lambdas, and Load Balancers are completely untouched.

---

## 🧱 Resource Block

> **Resource = "Build me a house" 🏗️** — creates a new AWS resource

### Syntax

```hcl
resource "TYPE" "NAME" {
  argument = value
}
```

### TYPE vs NAME

| Field | Standard? | Examples |
|-------|-----------|---------|
| `TYPE` | Yes — AWS standard | `aws_instance`, `aws_s3_bucket`, `aws_iam_user` |
| `NAME` | No — anything you want | `my_fav_server`, `r15_instance`, `whatever_bro` |

- **Indentation:** Spaces
- **Syntax:** Standard HCL format

### Prod Safety — Prevent Accidental Destroy

```hcl
lifecycle {
  prevent_destroy = true
}
```

> Add this to any critical resource to block accidental `terraform destroy`.

---

## 🔍 Data Block

> **Data = "Find me an existing house" 🔍** — fetches info only, creates nothing

### Syntax

```hcl
data "aws_vpc" "existing" {
  id = "vpc-12345678"
}

# Use its CIDR block
output "vpc_cidr" {
  value = data.aws_vpc.existing.cidr_block
}
```

Reference pattern: `data.TYPE.NAME.attribute`

### Resource vs Data — Quick Difference

| | Resource | Data |
|-|----------|------|
| **Does** | Creates/manages infra | Reads existing infra |
| **Analogy** | Build a house 🏗️ | Find an address 🔍 |
| **Creates AWS resource?** | ✅ Yes | ❌ No |

---

*Add more notes below 👇*
