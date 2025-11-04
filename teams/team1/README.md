# Team 1 — Open Source Mentor Bot

Follow these steps to build and run your service with Podman.

## 1) Prepare
- Copy templates into this folder:
  ```
  cp ../../templates/Containerfile.template ./Containerfile
  cp ../../templates/container.env.template ./.env
  ```
- Edit `.env` to set registry, routing, and LiteMAAS values.

## 2) Build and run
```
podman login $REGISTRY_HOST
podman-compose build
podman-compose up -d
```

### Ports and routing
- Configure compose to map container `8080` to host `18080`:
  ```yaml
  ports:
    - "18080:8080"  # host:container
  ```
- Traefik maps `team1.<IP>.sslip.io:80` to `127.0.0.1:18080`.

## 3) Access your app
```
http://team1.<IP>.sslip.io
```

## Notes
- Read the Hackathon FAQ: `../../docs/FAQ.md`
- Update `compose.yaml` labels if organizers enable Traefik label routing
- Keep this README updated with architecture notes and API endpoints

## Demo Day Checklist
- [ ] App accessible at `http://team1.<IP>.sslip.io`
- [ ] README explains purpose and simple architecture
- [ ] Logs are clean: `podman-compose logs -f`
- [ ] Red Hat values: note concrete examples in README
- [ ] 5-minute demo script rehearsed
- [ ] Be ready to answer: "What would you do with more time?"
