# 1. 베이스 이미지 선택 (가벼운 Nginx 서버 사용)
FROM nginx:alpine

# 2. 내가 만든 index.html을 컨테이너 내부의 서버 경로로 복사
COPY index.html /usr/share/nginx/html/index.html

# 3. 80번 포트를 열어주겠다고 명시
EXPOSE 80

# 4. 컨테이너 시작 시 실행될 명령어 (Nginx 실행)
CMD ["nginx", "-g", "daemon off;"]