# Notes on importing state on aws with terraform


Mike Preston, [20.04.18 18:26]
/Mikes-MacBook-Pro:aws-r53-terraform mike$/ terraform plan -var-file=terraform.tfvar
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


—----------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + aws_route53_record.dev-ns
      id:               <computed>
      allow_overwrite:  "true"
      fqdn:             <computed>
      name:             "dev.wwff.tech"
      records.#:        <computed>
      ttl:              "30"
      type:             "NS"
      zone_id:          "${aws_route53_zone.main.zone_id}"

  + aws_route53_zone.dev
      id:               <computed>
      comment:          "Managed by Terraform"
      force_destroy:    "false"
      name:             "dev.wwff.tech"
      name_servers.#:   <computed>
      tags.%:           "1"
      tags.Environment: "dev"
      vpc_region:       <computed>
      zone_id:          <computed>

  + aws_route53_zone.main
      id:               <computed>
      comment:          "Managed by Terraform"
      force_destroy:    "false"
      name:             "wwff.tech"
      name_servers.#:   <computed>
      vpc_region:       <computed>
      zone_id:          <computed>


Plan: 3 to add, 0 to change, 0 to destroy.

Mike Preston, [20.04.18 18:26]
This is what happens if you have no state...

Mike Preston, [20.04.18 18:26]
so if we do:
Mikes-MacBook-Pro:aws-r53-terraform mike$ terraform import -var-file=terraform.tfvar aws_route53_zone.main Z3NCX1W0K7QOAD
aws_route53_zone.main: Importing from ID "Z2NCX1W0K7QOAD"...
aws_route53_zone.main: Import complete!
  Imported aws_route53_zone (ID: Z3NCX1W0K7QOAD)
aws_route53_zone.main: Refreshing state... (ID: Z32NCX1W0K7QOAD)

Import successful!

The resources that were imported are shown above. These resources are now in
your Terraform state and will henceforth be managed by Terraform.

Mike Preston, [20.04.18 18:27]
Mikes-MacBook-Pro:aws-r53-terraform mike$ terraform import -var-file=terraform.tfvar aws_route53_zone.dev Z1TPEIT8XJDQ9X

aws_route53_zone.dev: Importing from ID "Z5TPEIT8XJDQ9X"...
aws_route53_zone.dev: Import complete!
  Imported aws_route53_zone (ID: Z1TPEIT8XJDQ9X)
aws_route53_zone.dev: Refreshing state... (ID: Z5TPEIT8XJDQ9X)

Import successful!

The resources that were imported are shown above. These resources are now in
your Terraform state and will henceforth be managed by Terraform.

Mike Preston, [20.04.18 18:27]
We get desired behaviour...

Mike Preston, [20.04.18 18:27]
Mikes-MacBook-Pro:aws-r53-terraform mike$ terraform plan -var-file=terraform.tfvar
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.

aws_route53_zone.dev: Refreshing state... (ID: Z5TPEIT8XJDQ9X)
aws_route53_zone.main: Refreshing state... (ID: Z2NCX1W0K7QOAD)

—----------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create
  ~ update in-place

Terraform will perform the following actions:

  + aws_route53_record.dev-ns
      id:                 <computed>
      allow_overwrite:    "true"
      fqdn:               <computed>
      name:               "dev.wwff.tech"
      records.#:          "4"
      records.1464508935: "ns-1910.awsdns-46.co.uk"
      records.3199777002: "ns-165.awsdns-20.com"
      records.3961330954: "ns-1445.awsdns-52.org"
      records.4009312083: "ns-853.awsdns-42.net"
      ttl:                "30"
      type:               "NS"
      zone_id:            "Z5NCX1W0K7QOAD"

  ~ aws_route53_zone.dev
      force_destroy:      "" => "false"

  ~ aws_route53_zone.main
      comment:            "test zone" => "Managed by Terraform"
      force_destroy:      "" => "false"


Plan: 1 to add, 2 to change, 0 to destroy.

Mike Preston, [20.04.18 18:28]
so we can make it work even if the zones are created outside of terraform
