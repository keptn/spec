# Uniform (DRAFT)

> **Important Note: This spec is not implemented yet. Consider it as a proposal.**

A `uniform` describes the services that listen to Keptn channels for events, i.e., it defines which service subscribes to which channel. Configuration parameters can be passed either a environment variable or references to config maps.

```yaml
---
apiVersion: sh.keptn/v1alpha1
kind: Uniform
metadata:
  name: bookinfo-uniform
  namespace: keptn
spec:
  services:
  - name: slack-trail
    image: keptn/slack-service:0.1.2
    env:
    - name: SLACK_WEBHOOK
      value: "https://hooks.slack.com/services/xyz
    subscribedchannels:
    - new-artifact
    - configuration-changed
    - deployment-done
    - tests-done
    - evaluation-done
    - problem
  - name: gitops-operator
    image: keptn/github-service:0.3.4
    env:
    - name: GITHUB_USER
      value: "johndoe"
    - name: GITHUB_TOKEN
      valueFrom:
        secretKeyRef:
            name: github-token
            key: GITHUB_TOKEN
    subscribedchannels:
    - new-artifact
  - name: cd-operator
    image: keptn/jenkins-service:0.1.1
    subscribedchannels:
    - configuration-changed
    - deployment-done
    - tests-done
    - evaluation-done
  - name: automated-operations
    image: keptn/servicenow-service:0.2.1
    subscribedchannels:
    - problem
```