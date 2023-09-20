

## docker build -t myimage:latest .


FROM node:18 as sdk

WORKDIR /app

RUN  npm install -g @angular/cli

COPY ./Hackathon-angular/package.json /app

RUN npm install

FROM node:18 as builder

WORKDIR /app

COPY --from=sdk /app /app

COPY ./Hackathon-angular /app

RUN npm run build

FROM nginx:1.25.2

WORKDIR /app

RUN rm -rf /usr/share/nginx/html/*

COPY --from=builder /app/dist/Hackathon-angular /usr/share/nginx/html

EXPOSE 80

ENTRYPOINT /bin/sh -c "nginx -g 'daemon off;'"
