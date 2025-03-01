# 🔹 Imagen base oficial de AWS Lambda para Python 3.9
FROM public.ecr.aws/lambda/python:3.9

# 🔹 Copiar dependencias e instalarlas en el contenedor
COPY lambdas/lambda_handler/requeriments.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 🔹 Copiar el código fuente al contenedor
COPY lambdas/lambda_handler/ /var/task/

# 🔹 Especificar el handler de la función Lambda
CMD ["lambda_handler.lambda_handler"]
