FROM nginx:alpine

# Copia a configuração customizada do Nginx
COPY nginx.conf /etc/nginx/nginx.conf

# Copia sua página HTML para dentro da imagem
COPY index.html /user/share/nginx/html/index.html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
