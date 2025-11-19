
### n8n
- https://n8n.io/

### 로컬 실행
```shell
docker run -it --rm \
 --name n8n \
 -p 5678:5678 \
 -v n8n_data:/home/node/.n8n \
 docker.n8n.io/n8nio/n8n
```

### 접속
- http://localhost5678
- 가입 후 로그인
