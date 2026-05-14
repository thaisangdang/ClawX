---
id: fix-windows-gateway-heartbeat-recovery
title: Recover unresponsive Gateway on Windows heartbeat timeout
scenario: gateway-backend-communication
taskType: runtime-bridge
intent: Restart the Gateway on Windows after lenient consecutive heartbeat misses instead of leaving a running but unresponsive process that causes all RPC calls to time out.
touchedAreas:
  - harness/specs/tasks/fix-windows-gateway-heartbeat-recovery.md
  - electron/gateway/manager.ts
  - tests/unit/gateway-manager-heartbeat.test.ts
expectedUserBehavior:
  - On Windows, when Gateway stops responding to pings and RPCs for the configured lenient heartbeat window, ClawX restarts it automatically.
  - Windows keeps the longer heartbeat interval and miss threshold to avoid false positives.
  - Initial startup still defers heartbeat recovery while waiting for gateway.ready within the grace window.
requiredProfiles:
  - fast
  - comms
requiredRules:
  - gateway-readiness-policy
  - renderer-main-boundary
  - backend-communication-boundary
  - api-client-transport-policy
  - comms-regression
requiredTests:
  - tests/unit/gateway-manager-heartbeat.test.ts
acceptance:
  - Renderer does not add direct IPC calls.
  - Renderer does not fetch Gateway HTTP directly.
  - Windows no longer logs heartbeat recovery skipped solely because platform=win32 after max heartbeat misses.
  - Windows uses the existing restart path after max heartbeat misses when autoReconnect is enabled and Gateway state is running.
  - Heartbeat restart remains skipped when autoReconnect is disabled or Gateway is not running.
docs:
  required: false
---
