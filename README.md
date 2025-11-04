# CI/CD Strategy for CineLog-App

## Full Pipeline Flow

1. 📥 Developer commits code to a feature branch
2. 🔄 Opens Pull Request to `main`
3. 🧪 GitHub Actions runs:
   - `npm run lint`
   - `npm run test:ci`
   - `npm run build`
4. ✅ If all steps pass → PR can be merged to `main`
5. 🏷️ To deploy to production:
   ```bash
   git tag -a v1.0.0 -m "Release v1.0.0"
   git push origin v1.0.0



# Deploy Strategy: Blue-Green Deployment

## What is Blue-Green Deployment?

Blue-Green Deployment is a **release strategy** that reduces downtime and risk by maintaining **two identical production environments**:

- **Blue**: the current live version of the application.
- **Green**: the new version being deployed.

Initially, all user traffic is routed to **Blue**.  
When **Green** is ready, traffic is **switched instantly** from Blue to Green (e.g., via a load balancer).  
If issues arise, traffic can be **rolled back immediately** by switching back to Blue.

![Blue-Green Diagram](https://www.abtasty.com/wp-content/uploads/bluegreen1.jpg)

## Why Choose Blue-Green?

| Benefit | Explanation |
|--------|-------------|
| **Zero Downtime** | Users never experience service interruption. |
| **Instant Rollback** | If Green fails, switch back to Blue in seconds. |
| **Reduced Risk** | New version is fully tested before going live. |
| **Simplified Testing** | Green can be tested internally before switch. |

## How It Would Be Applied to CineLog-App

Although **GitHub Pages does not support Blue-Green**, this strategy **would be implemented** in a containerized environment as follows:

### Step 1: Build New Version
```bash
## Build Docker image for new version
docker build -t cinelog-app:v1.1.0 .
```

### Step 2: Deploy Green Environment
Using **Docker Compose** or **Kubernetes**:

- Deploy `cinelog-app:v1.1.0` alongside the current version (`v1.0.0`).
- Green runs on a different port or cluster but is **not public yet**.

### Step 3: Internal Validation
- Run **smoke tests** against Green.  
- Verify **performance**, **UI**, and **integrations**.

### Step 4: Traffic Switch
- Update the **load balancer** (e.g., Nginx, AWS ALB) to route **100% of traffic** to Green.  
- Users now see the **new version**.

### Step 5: Decommission Blue
- After confirmation, **shut down** the old version (`v1.0.0`).

### Simple example with docker compose
```bash
# docker-compose.green.yml
services:
  frontend-green:
    image: cinelog-app:v1.1.0
    # ... same config as blue
   ```
   Then switch traffic using an **nginx reverse proxy**:
 ```bash
# Switch this line to toggle versions
# proxy_pass http://blue;
proxy_pass http://green;
   ```
  
 ## Why Not Rolling or Canary?
**Rolling Update**: Best for stateful apps or large clusters; overkill for a static frontend.
**Canary Release:** Useful for gradual exposure (e.g., 10% of users); not needed for small, low-risk frontend changes.
Blue-Green is ideal for stateless, frontend applications like CineLog-App because:

- It’s simple to implement with containers.
- It provides maximum safety with minimal complexity.

### Conclusion
While this strategy is not executed in the current GitHub Pages setup, it represents a professional, production-grade deployment method that would be used in real-world enterprise environments. Understanding and documenting it demonstrates readiness to work with advanced DevOps practices.