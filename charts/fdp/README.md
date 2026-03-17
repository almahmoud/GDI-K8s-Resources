# FAIR Data Point Helm Chart

This Helm chart provides a flexible way to deploy the FAIR Data Point (FDP) system on any Kubernetes cluster. It includes all necessary components: FDP Server, FDP Client, MongoDB database, and GraphDB triple store.

## Prerequisites

- Kubernetes 1.19+
- Helm 3+
- PV provisioner support in the underlying infrastructure
- Traefik (optional, for gateway functionality)

## Installation

### Add Helm Repository (if needed)

```bash
# No external repository needed - this is a local chart
```

### Install the Chart

#### Development Environment

```bash
# Install with development values
helm install fdp ./helm -f ./helm/values-dev.yaml --namespace fdp --create-namespace
```

#### Staging Environment

```bash
# Install with staging values
helm install fdp ./helm -f ./helm/values-staging.yaml --namespace fdp --create-namespace
```

#### Production Environment

```bash
# Install with production values
helm install fdp ./helm -f ./helm/values-prod.yaml --namespace fdp --create-namespace
```

### Custom Installation

```bash
# Install with custom values
helm install fdp ./helm \
  --set global.environment=dev \
  --set server.image.tag=1.19 \
  --set mongodb.auth.rootPassword=mysecretpassword \
  --set graphdb.admin.password=mysecretpassword \
  --namespace fdp --create-namespace
```

## Configuration

The following table lists the configurable parameters of the FDP chart and their default values.

### Global Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `global.labels` | Additional labels to add to all resources | `{}` |
| `global.namespace` | Namespace to deploy to | `""` (current namespace) |
| `global.environment` | Environment name (dev, staging, prod) | `"dev"` |

### FDP Server Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `server.enabled` | Enable FDP Server | `true` |
| `server.replicaCount` | Number of replicas | `1` |
| `server.image.repository` | FDP Server image repository | `"fairdata/fairdatapoint"` |
| `server.image.tag` | FDP Server image tag | `"1.18"` |
| `server.image.pullPolicy` | Image pull policy | `"IfNotPresent"` |
| `server.service.type` | Service type | `"ClusterIP"` |
| `server.service.port` | Service port | `80` |
| `server.service.targetPort` | Target port | `8080` |
| `server.resources` | Resource requests/limits | See values.yaml |
| `server.env.clientUrl` | Client URL | Development URL |
| `server.env.persistentUrl` | Persistent URL | Example URL |
| `server.env.title` | Instance title | Development title |
| `server.env.description` | Instance description | Development description |
| `server.mongo.host` | MongoDB host | `"fdp-mongodb"` |
| `server.mongo.port` | MongoDB port | `27017` |
| `server.mongo.database` | MongoDB database | `"fdp"` |
| `server.graphdb.url` | GraphDB URL | `"http://fdp-graphdb:7200"` |
| `server.graphdb.repository` | GraphDB repository | `"fdp"` |
| `server.jwt.secretKey` | JWT secret key | `""` (must be set) |

### FDP Client Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `client.enabled` | Enable FDP Client | `true` |
| `client.replicaCount` | Number of replicas | `1` |
| `client.image.repository` | FDP Client image repository | `"fairdata/fairdatapoint-client"` |
| `client.image.tag` | FDP Client image tag | `"1.18"` |
| `client.image.pullPolicy` | Image pull policy | `"IfNotPresent"` |
| `client.service.type` | Service type | `"ClusterIP"` |
| `client.service.port` | Service port | `80` |
| `client.service.targetPort` | Target port | `80` |
| `client.resources` | Resource requests/limits | See values.yaml |
| `client.fdpHost` | FDP Server host | `"fdp-server:80"` |

### MongoDB Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `mongodb.enabled` | Enable MongoDB | `true` |
| `mongodb.useExternal` | Use external MongoDB | `false` |
| `mongodb.image.repository` | MongoDB image repository | `"mongo"` |
| `mongodb.image.tag` | MongoDB image tag | `"6.0"` |
| `mongodb.image.pullPolicy` | Image pull policy | `"IfNotPresent"` |
| `mongodb.architecture` | MongoDB architecture | `"standalone"` |
| `mongodb.auth.enabled` | Enable authentication | `true` |
| `mongodb.auth.rootUser` | Root username | `"admin"` |
| `mongodb.auth.rootPassword` | Root password | `""` (must be set) |
| `mongodb.auth.database` | Authentication database | `"fdp"` |
| `mongodb.persistence.enabled` | Enable persistence | `true` |
| `mongodb.persistence.storageClass` | Storage class | `"harvester-longhorn"` |
| `mongodb.persistence.size` | Storage size | `"10Gi"` |
| `mongodb.resources` | Resource requests/limits | See values.yaml |
| `mongodb.init.enabled` | Initialize with default users | `false` |
| `mongodb.init.users` | List of users to create | See values.yaml |
| `mongodb.cleanup.enabled` | Enable user cleanup hook | `false` |
| `mongodb.cleanup.usersToDelete` | List of user emails to delete | `[]` |

### GraphDB Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `graphdb.enabled` | Enable GraphDB | `true` |
| `graphdb.useExternal` | Use external GraphDB | `false` |
| `graphdb.image.repository` | GraphDB image repository | `"ontotext/graphdb"` |
| `graphdb.image.tag` | GraphDB image tag | `"10.7.6"` |
| `graphdb.image.pullPolicy` | Image pull policy | `"IfNotPresent"` |
| `graphdb.javaOpts` | Java options | `"-Xms1g -Xmx2g"` |
| `graphdb.persistence.enabled` | Enable persistence | `true` |
| `graphdb.persistence.storageClass` | Storage class | `"harvester-longhorn"` |
| `graphdb.persistence.size` | Storage size | `"5Gi"` |
| `graphdb.resources` | Resource requests/limits | See values.yaml |
| `graphdb.repository.id` | Repository ID | `"fdp"` |
| `graphdb.repository.title` | Repository title | `"FDP Repository"` |
| `graphdb.repository.readOnly` | Read-only repository | `false` |
| `graphdb.admin.username` | Admin username | `"admin"` |
| `graphdb.admin.password` | Admin password | `""` (must be set) |

### Gateway Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `gateway.enabled` | Enable gateway | `false` |
| `gateway.traefik.enabled` | Enable Traefik routes | `false` |
| `gateway.traefik.routes` | Traefik route configurations | See values-dev.yaml |

## Upgrading

```bash
# Upgrade to a new version
helm upgrade fdp ./helm -f ./helm/values-dev.yaml --namespace fdp

# Upgrade with new image version
helm upgrade fdp ./helm \
  --set server.image.tag=1.19 \
  --set client.image.tag=1.19 \
  -f ./helm/values-dev.yaml \
  --namespace fdp
```

## Uninstalling

```bash
# Uninstall the release
helm uninstall fdp --namespace fdp

# Delete the namespace (optional)
kubectl delete namespace fdp
```

## Secrets Management

### Providing Secrets

Secrets can be provided in several ways:

1. **Via values files** (not recommended for production):
   ```yaml
   server:
     jwt:
       secretKey: "my-secret-key"
   mongodb:
     auth:
       rootPassword: "my-mongo-password"
   graphdb:
     admin:
       password: "my-graphdb-password"
   ```

2. **Via Helm command line** (better for CI/CD):
   ```bash
   helm install fdp ./helm \
     --set server.jwt.secretKey="$(cat jwt-secret.txt)" \
     --set mongodb.auth.rootPassword="$(cat mongo-password.txt)" \
     --set graphdb.admin.password="$(cat graphdb-password.txt)" \
     -f ./helm/values-dev.yaml \
     --namespace fdp
   ```

3. **Using external secrets manager** (recommended for production):
   - Use tools like HashiCorp Vault, AWS Secrets Manager, or Azure Key Vault
   - Integrate with Helm using the [External Secrets Operator](https://external-secrets.io/)

### Generating Secure Secrets

```bash
# Generate a secure JWT secret (128 characters)
openssl rand -base64 96 | tr -d '\n'

# Generate secure passwords
openssl rand -base64 16 | tr -d '\n'
```

## Troubleshooting

### Debugging Installations

```bash
# Dry run to validate templates
helm template fdp ./fdp-helm -f ./fdp-helm/values-dev.yaml --debug

# Check release status
helm status fdp --namespace fdp

# Get pod logs
kubectl logs -l app.kubernetes.io/name=fdp --namespace fdp

# Describe pods
kubectl describe pods -l app.kubernetes.io/name=fdp --namespace fdp
```

### Common Issues

**Issue: GraphDB initialization fails**
- Check the init container logs: `kubectl logs fdp-graphdb-0 -c generate-repo-config --namespace fdp`
- Verify the repository configuration is correct
- Ensure GraphDB has enough resources

**Issue: MongoDB connection fails**
- Verify credentials in the secret
- Check network connectivity between FDP Server and MongoDB
- Ensure MongoDB has enough resources

**Issue: Gateway routes not working**
- Verify Traefik is installed and running
- Check IngressRoute resources: `kubectl get ingressroutes --namespace fdp`
- Verify hostnames and paths in the gateway configuration

## Architecture

The Helm chart deploys the following components:

1. **FDP Server**: Java Spring Boot application providing the FAIR Data Point API
2. **FDP Client**: Web interface for interacting with the FDP
3. **MongoDB**: Database for storing metadata and configuration
4. **GraphDB**: Triple store for RDF data
5. **Gateway** (optional): Traefik routes for external access

All components are properly labeled and can be discovered using:
```bash
kubectl get all -l app.kubernetes.io/name=fdp --namespace fdp
```

## Development

### Testing Changes

```bash
# Validate templates
helm lint ./helm

# Dry run installation
helm template fdp ./helm -f ./helm/values-dev.yaml

# Test in a development namespace
helm install fdp-test ./helm -f ./helm/values-dev.yaml --namespace fdp-test --create-namespace
```

### Adding New Components

To add new components to the chart:

1. Create new template files in the appropriate subdirectory
2. Add configuration options to `values.yaml`
3. Update the documentation
4. Test the new component thoroughly

### MongoDB User Initialization and Cleanup

The chart supports initializing MongoDB with default users and/or cleaning up specific users after FDP deployment completes. Both operations are handled by a single post-install/post-upgrade hook Job that runs **after FDP is fully deployed**.

#### How It Works

- **Post-Install Hook**: The job runs after successful FDP Client deployment
- **Consolidated Operation**: All user management (deletion and creation) happens in a single MongoDB session
- **No External Scripts**: All operations are defined inline within the Helm values

#### Enabling User Initialization

To create default users when MongoDB is initialized, set:

```yaml
mongodb:
  init:
    enabled: true
    users:
      - uuid: "f47ac10b-58cc-4372-a567-0e02b2c3d479"
        firstName: "Admin"
        lastName: "User"
        email: "admin@example.com"
        passwordHash: "$2b$10$M1tsK2jrPBSfmUEYF63l4.mZM4R0u3SKgsiTldp.xjF2LxoLki.R2"
        role: "ADMIN"
      - uuid: "131b32be-37d2-49df-b67c-be59aefafde7"
        firstName: "Test"
        lastName: "User"
        email: "test@example.com"
        passwordHash: "$2b$10$u0cuYZwkBaQEqZ.ooHflTeaJZi73bCkNPmpuMo7rFaAnGjAoi2Lpu"
        role: "USER"
```

#### Enabling User Cleanup

To delete specific users after FDP Client deployment (useful for removing default test users or users from previous deployments), set:

```yaml
mongodb:
  cleanup:
    enabled: true
    usersToDelete:
      - "albert.einstein@example.com"
      - "nikola.tesla@example.com"
```

#### Using Both Features Together

Both can be enabled simultaneously. The job will:
1. Delete the specified cleanup users
2. Delete all existing users
3. Insert the new users from the init configuration

```yaml
mongodb:
  init:
    enabled: true
    users: [...]
  cleanup:
    enabled: true
    usersToDelete: [...]
```

#### Important Notes

- **Execution Order**: When both are enabled, cleanup happens first, then initialization
- **No Plaintext Passwords Stored**: Only password hashes are used; the `password` field in values.yaml is optional and for reference only
- **Idempotent**: The job can be safely re-run (it automatically cleans up previous Job runs)
- **Waits for FDP**: The job only runs after all FDP components are deployed and ready

#### Generating Bcrypt Password Hashes

The FDP system uses bcrypt for password hashing. You need to generate bcrypt hashes for each user's password and populate the `passwordHash` field.

**Important:** The `password` field is optional and only for documentation/reference. The system only uses the `passwordHash` field for authentication.

**Quick Docker Command (Recommended):**

```bash
docker run --rm python:3.11-slim bash -c "pip install -q bcrypt && python3 -c \"import bcrypt; print(bcrypt.hashpw(b'your-password-here', bcrypt.gensalt(10)).decode())\""
```

**Example:**
```bash
$ docker run --rm python:3.11-slim bash -c "pip install -q bcrypt && python3 -c \"import bcrypt; print(bcrypt.hashpw(b'AdminPassword123!', bcrypt.gensalt(10)).decode())\""
$2b$10$M1tsK2jrPBSfmUEYF63l4.mZM4R0u3SKgsiTldp.xjF2LxoLki.R2
```

**If Python is installed locally:**

```bash
python3 -c "import bcrypt; print(bcrypt.hashpw(b'your-password-here', bcrypt.gensalt(10)).decode())"
```

**Important Security Notes:**
- Always generate bcrypt hashes with a cost factor of at least 10
- In production, use secure methods (not online generators)
- Never commit cleartext passwords to version control
- Each run produces a different hash (due to random salt) - this is normal and secure!
- Always change the default passwords in production

#### Generating UUIDs

Each user needs a unique UUID (version 4). You can generate these using minimal standard tools:

**Example: Using Python (no external dependencies):**

```bash
$ python3 -c "import uuid; print(uuid.uuid4())"
f47ac10b-58cc-4372-a567-0e02b2c3d479
```

**Using Linux kernel:**

```bash
cat /proc/sys/kernel/random/uuid
```


#### User Cleanup

After deployment, you can optionally delete specific users using the cleanup hook:

```yaml
mongodb:
  cleanup:
    enabled: true
    usersToDelete:
      - "albert.einstein@example.com"
      - "nikola.tesla@example.com"
```

This is useful if you want to remove default users while keeping custom users created during initialization.

### Password Security Guidelines

The chart includes password validation to prevent common issues with special characters that can break database connections, particularly with MongoDB.

#### Problematic Characters

Avoid using these characters in passwords as they may cause connection issues:
- `%` (percentage) - Can break URI encoding
- `\` (backslash) - Can cause escaping issues  
- `"` (double quote) - Can break YAML/JSON parsing
- `$` (dollar sign) - Can cause template interpolation issues
- `'` (single quote) - Can break shell commands

#### Password Validation Configuration

The chart provides configurable password validation:

```yaml
mongodb:
  auth:
    passwordValidation:
      # strict: true  # Set to true to fail installation on problematic passwords
      strict: false  # Shows warnings but allows installation
      problematicChars:
        - "%"
        - "\\"
        - "\""
        - "$"
        - "'"
      minLength: 12
```

#### Generating Safe Passwords

Use these commands to generate safe passwords:

```bash
# Generate a 32-character safe password (recommended)
openssl rand -base64 32 | tr -d '/+' | head -c 32 ; echo

# Generate a password with only alphanumeric and safe special characters
openssl rand -base64 32 | tr -d '/+=%\\"$'"'" | head -c 32 ; echo

# Generate a password and validate it
PASSWORD=$(openssl rand -base64 32 | tr -d '/+=%\\"$'"'" | head -c 32 ; echo)
echo "Generated password: $PASSWORD"
```

#### Troubleshooting Password Issues

If you encounter connection issues:

1. **Check for special characters**: Ensure your password doesn't contain `%`, `\`, `"`, `$`, or `'`
2. **Test with a simple password**: Try a simple alphanumeric password to isolate the issue
3. **Check logs**: `kubectl logs -l app.kubernetes.io/name=fdp-mongodb --namespace fdp`
4. **Enable strict mode**: Set `mongodb.auth.passwordValidation.strict: true` to catch issues early

#### Best Practices

- Use passwords of at least 12 characters
- Include a mix of uppercase, lowercase, numbers, and safe special characters
- Avoid characters that have special meaning in URLs, shells, or regular expressions
- Rotate passwords regularly in production environments

## Support

For issues with the Helm chart:
- Check the [FAIR Data Point documentation](https://fairdatapoint.org/)
- Report issues to the chart maintainers
- Consult the Kubernetes and Helm documentation

## License

This Helm chart is licensed under the same license as the FAIR Data Point software.

## Values Reference

For a complete reference of all available configuration options, see the `values.yaml` file and the environment-specific values files (`values-dev.yaml`, `values-staging.yaml`, `values-prod.yaml`).