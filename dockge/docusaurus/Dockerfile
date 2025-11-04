FROM node:lts

RUN npx create-docusaurus@latest /app classic -j && chown -R node:node /app

USER node

WORKDIR /app

RUN cd /app && yarn install

COPY . .

CMD ["yarn", "start", "--host", "0.0.0.0", "--poll", "1000"]
