# Configuring Node port

service.yaml
```groovy
apiVersion: v1
kind: Service
metadata:
  name: my-app
  namespace: default
spec:
  type: NodePort  # Ensures external access via a specific port
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80       # Service port inside the cluster
      targetPort: 80  # The container's port
      nodePort: 30391   # Externally accessible port
```