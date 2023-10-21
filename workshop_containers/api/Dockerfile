FROM node:14
WORKDIR .
COPY . ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]