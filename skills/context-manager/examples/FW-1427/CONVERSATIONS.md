# Conversations

## 2026-05-21 - Firmware and customer engineers, ticket comments

The issue was described as a need to provide enough PCIe BDF resources for four NVMe shelves, estimated at about 30 per shelf. The origin of a reported 40-identifier limit was unclear. Firmware engineers suggested the per-root-port extra-bus setting, and the customer reported that it did not help.

**Decision:** Investigate platform bus allocation instead of treating the setup padding limit as the root cause.

## 2026-06-08 - Customer storage engineer, direct chat

The required total was clarified as 96 drives: 48 per socket. Seventy-two drives had enumerated when three shelves were connected across different buses, and 48 drives on one socket already worked in parallel without firmware changes. The failure was specifically two shelves cascaded behind one slot.

**Decision:** Narrow the issue to the cascaded single-slot topology; do not modify the working parallel topology.

## 2026-06-08 - Firmware engineer, task update after customer discussion

The customer identified unused high-speed links on two neighbouring slot groups. The proposed direction was to stop reserving unnecessary bus capacity there and make it available to the cascade-shelf slot, initially as forced policy and later behind setup controls.

**Decision:** Reclaim bus capacity from lightly used neighbouring I/O stacks without physically disabling ports.

## 2026-07-20 - Customer hardware team, coordination call

The customer planned to test an additional shelf-side readiness delay after captures showed intermittent endpoint loss only on the second cascade layer.

**Decision:** Validate shelf-side delay independently from firmware endpoint recovery, then test both together so their effects remain distinguishable.
