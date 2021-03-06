# React+Redux+Rails Boilerplate
A skeleton project that runs:
- Rails
- React
- Redux
- React Router
- Terraform
- AWS CodeDeploy
- Nginx + Certbot

### Production Setup
To run the simple HTTP server, open `deploy/services/rails.service` and change the `-b` part of the `ExecStart` line to a port instead of a filepath.  
  
Running the full Nginx HTTPS server is more complicated. First you need to obtain an HTTPS certificate for your domain and setup Nginx:  
`sudo certbot certonly --nginx -d example.com -d www.example.com`
  
This will generate your certificate, and create an a configuration file for Nginx that can be found at `/etc/nginx/sites-available/default`. To configure it for your domain, replace this file with `deploy/nginx` in the repository and replace `EXAMPLE` with your domain. Make sure that both `Nginx.service` and 'rails.service' are running and the server should work with HTTPS.

### Terraform Setup
To use Terraform, open `terraform.tf` and change the variables name, username, and provider to your custom values. Make a note of the name, this will be used for CodeDeploy setup.

You also must create an EC2 Keypair with that name from your AWS console.

### CodeDeploy Setup
To setup CodeDeploy, start by opening `deploy/install_dependencies.sh` and `deploy/deploy.sh`. Quickly skim the file and switch any variables/names from `test` to whatever name you chose in the Terraform setup.  
  
After that, run `terraform apply` to spin up your infrastructure. Once that is complete (will take around 5 minutes), copy `deploy/image_install_script.sh` in the EC2 instance and run it. This will install CodeDeploy.  
  
While on the server, create a `secrets.env` file in `home/ubuntu`. Store something like this in it:  
```
    SECRET_KEY_BASE={YOUR_GENERATED_SECRET_KEY}
    PGUSER={YOUR_DATABASE_USERNAME}
    PGPASSWORD={YOUR_DATABASE_PASSWORD}
    PGHOST={YOUR_DATABASE_ADDRESS}
    PGPORT=5432
```  
`PGUSER`, `PGPASSWORD`, and `PGHOST` can be found in the output of the `terraform apply` command you ran previously. `SECRET_KEY_BASE` can be generated by running `rake secret`.  
  
With your instance setup, you can now run `deploy/deploy.sh` from your computer and it should all work.

### To-do
- Create ec2 and deployment roles with terraform
