provider "aws" {
  region = "us-east-1"
}
resource "aws_instance" "my_instance" {
  ami           = "ami-0e2c8caa4b6378d8c" # Use the appropriate AMI ID
  instance_type = "t2.micro"
  key_name      = "keyforterraform"
}

resource "aws_autoscaling_group" "asg" {
  desired_capacity     = 2
  max_size             = 3
  min_size             = 1
  vpc_zone_identifier  = ["subnet-09cbd3fafc401e072"] # Replace with your subnet ID
}

resource "aws_launch_template" "lc" {
  image_id      = "ami-0e2c8caa4b6378d8c"

  instance_type = "t2.micro"
  key_name      = "keyforterraform"

}

resource "aws_lb" "my_lb" {
  name               = "my-load-balancer"
  internal           = false
  load_balancer_type = "application"
  subnets            = ["subnet-09cbd3fafc401e072", "subnet-0573ba8b0bf974a90"]
}

resource "aws_route53_record" "dns" {
  zone_id = "Z04859902TQGQRD3RZGXU" # Replace with your Route 53 hosted zone ID
  name    = "samsorzone10.com"
  type    = "A"

  alias {
    name                   = aws_lb.my_lb.dns_name
    zone_id                = aws_lb.my_lb.zone_id
    evaluate_target_health = false
  }
}
