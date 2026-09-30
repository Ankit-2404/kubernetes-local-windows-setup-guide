# Kubernetes Concepts --- Beginner Notes

## Cluster

The complete Kubernetes environment managed as one system.

## Node

A machine/environment on which Kubernetes workloads run.

## Pod

The smallest deployable Kubernetes unit. A Pod contains one or more
containers.

## Container

An isolated runtime environment for an application.

## Docker

Builds, packages, and runs containers.

## Kubernetes

Orchestrates and manages containerized workloads.

## kind

Kubernetes IN Docker. Creates local Kubernetes clusters using Docker
containers as nodes.

## kubectl

The Kubernetes CLI used to communicate with the Kubernetes API server.

## Control Plane

The management side of Kubernetes. Important components include the API
server, etcd, scheduler, and controller manager.

## Service

A stable network endpoint for accessing a group of Pods.

## Deployment

A Kubernetes controller used to manage replicated application Pods and
support rolling updates.

## YAML

A human-readable format used to describe Kubernetes desired state.

## CoreDNS

Provides DNS-based service discovery inside the cluster.

## Context

A kubectl configuration entry that identifies which Kubernetes
cluster/user/namespace kubectl should use.
