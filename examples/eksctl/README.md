# Crear una VPC

```
aws ec2 create-vpc --cidr-block 10.0.0.0/16
```

# Guardar el ID de la VPC en una variable
```
VPC_ID=$(aws ec2 describe-vpcs --filters "Name=cidr-block,Values=10.0.0.0/16" --query "Vpcs[0].VpcId" --output text)
```

# Crear dos subnets en diferentes zonas de disponibilidad
```
SUBNET_1=$(aws ec2 create-subnet --vpc-id $VPC_ID --cidr-block 10.0.1.0/24 --availability-zone us-east-1a --query "Subnet.SubnetId" --output text)
SUBNET_2=$(aws ec2 create-subnet --vpc-id $VPC_ID --cidr-block 10.0.2.0/24 --availability-zone us-east-1b --query "Subnet.SubnetId" --output text)
```

# Crear una gateway de internet
```
GATEWAY_ID=$(aws ec2 create-internet-gateway --query "InternetGateway.InternetGatewayId" --output text)
```

# Asociar la gateway de internet a la VPC
```
aws ec2 attach-internet-gateway --vpc-id $VPC_ID --internet-gateway-id $GATEWAY_ID
```

# Crear una tabla de rutas y asociarla con las subnets
```
ROUTE_TABLE_ID=$(aws ec2 create-route-table --vpc-id $VPC_ID --query "RouteTable.RouteTableId" --output text)
aws ec2 associate-route-table --route-table-id $ROUTE_TABLE_ID --subnet-id $SUBNET_1
aws ec2 associate-route-table --route-table-id $ROUTE_TABLE_ID --subnet-id $SUBNET_2
```

# Crear una ruta predeterminada para la gateway de internet
```
aws ec2 create-route --route-table-id $ROUTE_TABLE_ID --destination-cidr-block 0.0.0.0/0 --gateway-id $GATEWAY_ID
```

# Crear un Security Group
```
aws ec2 create-security-group --group-name my-security-group --description "Security group for EKS" --vpc-id $VPC_ID
```

# Create EKS Cluster with eksctl
```
eksctl create cluster -f config.yaml
```