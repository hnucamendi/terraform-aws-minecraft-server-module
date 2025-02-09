# Minecraft Server Terraform Module

This Terraform module automates the deployment of a **Minecraft server** on AWS using an **EC2 instance** with **Docker**. It supports both **Vanilla** and **FTB (Feed The Beast) modpacks**.

## **Usage**

To use this module, include it in your Terraform configuration:

```hcl
module "minecraft_server" {
  source = "./modules/minecraft_server"
  
  app_name               = "my-minecraft-server"
  vpc_id                 = "vpc-0123456789abcdef"
  instance_type          = "t3.medium"
  isModpack              = false
  minecraft_memory_G     = 4
  minecraft_max_players  = 10
  security_group_ingress_rules = {
    "minecraft" = {
      description = "Allow Minecraft access"
      from_port   = 25565
      to_port     = 25565
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    }
  }
}
```

## **Variables**

| Variable | Type | Default | Description |
|----------|------|---------|-------------|
| `app_name` | `string` | **Required** | Name of the application, used for naming resources like security groups and IAM roles. |
| `vpc_id` | `string` | **Required** | The ID of the VPC where the instance will be launched. |
| `ami` | `string` | `ami-06b21ccaeff8cd686` | The Amazon Machine Image (AMI) ID used for the EC2 instance. |
| `instance_type` | `string` | **Required** | AWS EC2 instance type (e.g., `t3.medium`). |
| `isModpack` | `bool` | `false` | Whether to install an FTB modpack. If `true`, `ftb_modpack_id` and `ftb_modpack_version` must be provided. |
| `minecraft_server_port` | `number` | `25565` | Port for the Minecraft server. |
| `minecraft_server_type` | `string` | `Vanilla` | The type of Minecraft server (e.g., `Vanilla`, `Forge`, `FTBA`). |
| `ftb_modpack_id` | `number` | `0` | The ID of the FTB modpack (only used if `isModpack` is `true`). |
| `ftb_modpack_version` | `number` | `0` | The version ID of the FTB modpack (only used if `isModpack` is `true`). |
| `minecraft_memory_G` | `number` | **Required** | Amount of memory (RAM) allocated to the Minecraft server, in GB. |
| `minecraft_timezone` | `string` | `America/New_York` | The timezone for the server. |
| `minecraft_max_players` | `number` | **Required** | Maximum number of players allowed on the server. |
| `minecraft_motd` | `string` | `Minecraft Server` | Message of the Day (MOTD) displayed in the server list. |
| `minecraft_difficulty_level` | `number` | `1` | Difficulty level (0 = Peaceful, 1 = Easy, 2 = Normal, 3 = Hard). |
| `minecraft_ops_list` | `string` | **Required** | List of Minecraft server operators (comma-separated). |
| `minecraft_rcon_cmds_last_disconnect` | `string` | **Required** | RCON commands to execute when the last player disconnects. |
| `public_key_pair_path` | `string` | `~/.ssh/id_ed25519.pub` | Path to the public SSH key used for accessing the EC2 instance. |
| `security_group_ingress_rules` | `map(object)` | **Required** | Ingress (incoming) rules for the security group. Define port ranges, CIDR blocks, and descriptions. |
| `security_group_egress_rules` | `map(object)` | Allows all traffic by default | Egress (outgoing) rules for the security group. |

## **Security Group Configuration**

To allow Minecraft access from anywhere and restrict SSH to your IP:

```hcl
security_group_ingress_rules = {
  "minecraft" = {
    description = "Allow Minecraft access"
    from_port   = 25565
    to_port     = 25565
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  "ssh" = {
    description = "Allow SSH access"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["192.168.1.100/32"] # Replace with your actual IP
  }
}
```

## **Outputs**
After running `terraform apply`, the following outputs will be available:

| Output | Description |
|--------|-------------|
| `instance_id` | The ID of the EC2 instance. |
| `instance_public_ip` | The public IP address of the instance. |

## **Deployment Instructions**

1. **Initialize Terraform:**
   ```sh
   terraform init
   ```
2. **Plan Deployment:**
   ```sh
   terraform plan
   ```
3. **Apply Deployment:**
   ```sh
   terraform apply -auto-approve
   ```
4. **Retrieve the server's public IP:**
   ```sh
   terraform output instance_public_ip
   ```
5. **Connect to the server via SSH:**
   ```sh
   ssh -i ~/.ssh/id_ed25519 ec2-user@<instance_public_ip>
   ```
6. **Manage the Minecraft server:**
   ```sh
   run-minecraft-server
   ```

## **Notes**
- Ensure your **security group allows access** to the Minecraft server port.
- If using an **FTB modpack**, set `isModpack = true` and provide a valid `ftb_modpack_id` and `ftb_modpack_version`.
- Make sure the **public key exists at `~/.ssh/id_ed25519.pub`** or update `public_key_pair_path` accordingly.

---

This module simplifies **Minecraft server deployment on AWS**, making it easy to configure and launch with Terraform. 🚀


