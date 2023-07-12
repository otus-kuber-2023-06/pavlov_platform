FROM nginx:stable-bullseye-perl
LABEL "performer"="PavlovMaxim" "email"="<minized@gmail.com>"
RUN mkdir -p /app && chown -R nginx:nginx /app
ADD nginx.conf /etc/nginx/conf.d/default.conf
ADD index.html /app/
EXPOSE 8000
CMD ["nginx", "-g", "daemon off;"]