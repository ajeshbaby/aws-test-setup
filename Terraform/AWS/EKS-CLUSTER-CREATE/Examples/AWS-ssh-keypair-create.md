## SSH Keypair Create across any region



```aws ec2 create-key-pair --key-name test1 --query 'KeyMaterial' --output text > key.pem```  
```chmod 400 key.pem```  
```ssh username@PUBLICIP-OF-EC2Inst -i key.pem```   

