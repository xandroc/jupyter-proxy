# jupyter-proxy


Using NGINX to route traffic to a compute node hosting python Jupyter Notebook. Compute nodes are accessible via login node so each compute node uses a dynamically assigned port to enable traffic to multiple notebooks simultaneously.

Nginx needs to know what port to forward the request to so the notebook host is set up to inlcude the port number in the base url. For this example, we're using port 8888 on notebook launch,

    `start-notebook.sh --NotebookApp.base_url='/apps/jupyter/8888'`

Nginx uses regex matching in the location directive to forward the request to the right port number

```
    location ~ /apps/jupyter/([0-9][0-9][0-9][0-9])/(.*) {
        resolver 127.0.0.11;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";

        proxy_pass http://jupyter-host:$1/apps/jupyter/$1/$2;
    }
```

Run Proxy/Notebook:

1) `docker-compose up`
2) Go to http://localhost/apps/jupyter/8888/
