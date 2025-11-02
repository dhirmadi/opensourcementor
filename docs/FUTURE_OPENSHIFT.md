# Future: Migrating to OpenShift

This repository is structured to enable a smooth path from Podman Compose to OpenShift.

## Concepts Mapping
- Compose service -> Deployment + Service + Route
- `.env` -> ConfigMap/Secret
- Image build -> BuildConfig or external build + ImageStream

## Suggested Steps
1. Create a new project/namespace:
   ```
   oc new-project mentor-bot-team1
   ```
2. Build or import image:
   - Use `oc new-build` or push to an external registry and `oc import-image`
3. Create Deployment and Service:
   ```
   oc new-app --name=app <image-ref> -e KEY=VALUE
   oc expose service app
   ```
4. Configure environment via `ConfigMap`/`Secret`.
5. Verify the Route and scale replicas as needed.

## Notes
- Avoid hardcoding registry hosts
- Keep environment keys consistent with `.env.example`
- Add health endpoints for readiness/liveness

## Advanced Patterns (brief)
- ImageStreams for CI/CD triggers and rollout automation
- ResourceQuotas and LimitRanges to prevent noisy neighbors
- NetworkPolicies for zero-trust, least-privilege networking
- Consider Operators and RHOAI/KServe for AI workloads
