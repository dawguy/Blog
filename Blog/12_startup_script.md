# Startup Script

Decided to make life a little easier and create a repo for the startup.

setup.sh
```zsh
tmux new -d -s server "docker-compose up" &
cd ./reverse-proxy
tmux new -d -s proxy "docker-compose up" &
```

Below is the tree structure.

.
├── docker-compose.yml
├── reverse-proxy
│   ├── docker-compose.yml
│   └── nginx.conf
└── setup.sh


One thing that did stump me for 30 minutes was that the second tmux session never started. It ends up that it was because my docker-compose up had an issue (I copy pasted an incorrect volume for the nginx.conf). To debug make sure each docker-compose file works manually as that seemed like the issue for me.
