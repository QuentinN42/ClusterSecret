# ClusterSecret
![CI](https://github.com/zakkg3/ClusterSecret/workflows/CI/badge.svg) [![CII Best Practices](https://bestpractices.coreinfrastructure.org/projects/4283/badge)](https://bestpractices.coreinfrastructure.org/projects/4283)[![License](http://img.shields.io/:license-apache-blue.svg)](http://www.apache.org/licenses/LICENSE-2.0.html)

---

## Kubernetes ClusterSecret 
[*clustersecret.io*](https://clustersecret.io/)

Global inter-namespace cluster secrets - Secrets that work across namespaces  - Clusterwide secrets

ClusterSecret operator makes sure all the matching namespaces have the secret available. New namespaces, if they match the pattern, will also have the secret.
Any change on the ClusterSecret will update all related secrets. Deleting the ClusterSecret deletes "child" secrets (all cloned secrets) too.

Full documentation available at [https://clustersecret.io](https://clustersecret.io/)

<img src="https://github.com/zakkg3/ClusterSecret/blob/master/docs/clusterSecret.png" alt="Clustersecret diagram">

---

Here is how it looks like:

```yaml
Kind: ClusterSecret
apiVersion: clustersecret.io/v1
metadata:
  namespace: clustersecret
  name: default-wildcard-certifiate
matchNamespace:
  - prefix_ns-*
  - anothernamespace
avoidNamespaces:
  - supersecret-ns
data:
  tls.crt: BASE64
  tls.key: BASE64
```


## Use cases.


Use it for certificates, registry pulling credentials and so on.

when you need a secret in more than one namespace. you have to: 

1- Get the secret from the origin namespace.
2- Edit the  the secret with the new namespace.
3- Re-create the new secret in the new namespace. 


This could be done with one command:

```bash
kubectl get secret <secret-name> -n <source-namespace> -o yaml \
| sed s/"namespace: <source-namespace>"/"namespace: <destination-namespace>"/\
| kubectl apply -n <destination-namespace> -f -
```

Clustersecrets automates this. It keep track of any modification in your secret and it will also react to new namespaces. 


# installation

## Requirements

Current version 0.0.7 is tested for Kubernetes >= 1.19 up to 1.25

For older kubernes (<1.19) use the image tag "0.0.6" in  yaml/02_deployment.yaml

## tl;dr install

```bash
kubectl apply -f ./yaml
```
 
 
# quick start:

create a ClusterSecret object yaml like the one above, or in the example in yaml/Object_example/obj.yaml and apply it in your cluster `kubectl apply -f yaml/Object_example/obj.yaml`

The ClusterSecret operator will pick it up and will create the secret in every matching namespace:  match `matchNamespace` but not matching  `avoidNamespaces` RegExp's.

You can specify multiple matching or non-matching RegExp. By default it will match all, same as defining matchNamespace = * 

## Get the clustersecrets

```bash
$> kubectl get csec -n clustersecret
NAME            TYPE
global-secret
```

## Minimal example

```yaml
apiVersion: clustersecret.io/v1
kind: ClusterSecret
metadata:
  name: global-secret
  namespace: my-fav-namespce
data:
  username: MTIzNDU2Cg==
  password: Nzg5MTAxMTIxMgo=
```


## Digests

latest = 0.0.7

docker.io/flag5/clustersecret:

0.0.7 digest: sha256:c8dffeefbd3c8c54af67be81cd769e3c18263920729946b75f098065318eddb1
0.0.7_arm32: digest: sha256:ffac630417bd090c958c9facf50a31ba54e0b18c89ef52d8eec5c1326a5f20ad
# Roadmap:

Tag 0.0.8:

 - [ ] implement `source` to specify a source secret to sync instead of `data` field. (https://github.com/zakkg3/ClusterSecret/issues/3)
 

 
 * * *
 
# Support
 
 If you need support, start with the troubleshooting guide: Run it in debug mode.
 You can open issues and we will try to address them. 

 That said, if you have questions, or just want to establish contact, reach out one way or another. [https://flag5.com](https://flag5.com) || nico at flag5.com
