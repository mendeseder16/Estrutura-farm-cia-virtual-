# Estrutura-farm-cia-virtual-

Infraestrutura (Terraform)
Este código cria o "esqueleto": um banco de dados DynamoDB (para estoque e pedidos) e um bucket S3 (para as fotos dos remédios).
hcl
# main.tf
provider "aws" {
  region = "us-east-1"
}

# Banco de dados NoSQL para produtos e pedidos
resource "aws_dynamodb_table" "farmacia_db" {
  name           = "ProdutosFarmacia"
  billing_mode   = "PAY_PER_REQUEST"
  hash_key       = "ProdutoID"

  attribute {
    name = "ProdutoID"
    type = "S"
  }
}

# Armazenamento de imagens do catálogo
resource "aws_s3_bucket" "farmacia_storage" {
  bucket = "farmacia-virtual-assets-12345" # Nome único
}
Use o código com cuidado.

2. Backend (AWS Lambda - Node.js)
Este código simula uma função para listar um produto. Ele se conecta ao DynamoDB que criamos acima.
javascript
// index.js
const AWS = require('aws-sdk');
const dynamo = new AWS.DynamoDB.DocumentClient();

exports.handler = async (event) => {
    const params = {
        TableName: "ProdutosFarmacia",
        Key: {
            ProdutoID: event.pathParameters.id
        }
    };

    try {
        const data = await dynamo.get(params).promise();
        return {
            statusCode: 200,
            body: JSON.stringify(data.Item),
        };
    } catch (err) {
        return {
            statusCode: 500,
            body: JSON.stringify({ error: "Erro ao buscar produto" }),
        };
    }
};


3. Frontend Estático (HTML/S3)
Para hospedar o site no S3, você usaria um index.html simples que consome a API:
html
<!-- index.html -->
<!DOCTYPE html>
<html>
<body>
    <h1>Farmácia Cloud</h1>
    <div id="vitrine">Carregando produtos...</div>
    
    <script>
        // Aqui você conectaria com o endpoint do API Gateway
        console.log("Sistema pronto para conectar na AWS");
    </script>
</body>
</html>
