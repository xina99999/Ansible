**Ansible Monitoring Stack**

Mô tả
- Dự án Ansible này triển khai một bộ công cụ giám sát (Prometheus, Grafana, node_exporter, cadvisor, v.v.) và các exporter liên quan trên các host mục tiêu bằng Docker Compose templates.

**Cấu Trúc Dự Án**
- **File chính:** [site.yml](site.yml)
- **Inventory:** [hosts](hosts)
- **Biến nhóm:** [group_vars/monitor.yml](group_vars/monitor.yml)
- **Roles:**
  - [roles/cadvisor](roles/cadvisor) — thu thập container metrics
  - [roles/dcgm-exporter](roles/dcgm-exporter) — NVIDIA DCGM exporter cho GPU metrics
  - [roles/docker](roles/docker) — cài Docker / Docker Compose nếu cần
  - [roles/grafana](roles/grafana) — Grafana + provisioning
  - [roles/kafka_exporter](roles/kafka_exporter) — Kafka exporter
  - [roles/node_exporter](roles/node_exporter) — node_exporter cho hệ thống
  - [roles/nvidia-gpu-exporter](roles/nvidia-gpu-exporter) — exporter GPU tùy chỉnh
  - [roles/prometheus](roles/prometheus) — Prometheus + config

**Yêu cầu**
- Ansible >= 2.9
- Docker & Docker Compose trên các host mục tiêu (roles/docker sẽ hỗ trợ cài đặt nếu cần)
- (Nếu dùng GPU) Driver NVIDIA và NVIDIA Docker runtime cho các exporter dựa trên GPU

**Cài đặt & Chạy**
1. Kiểm tra inventory trong [hosts](hosts) và điều chỉnh `group_vars/monitor.yml` theo môi trường của bạn.
2. Chạy playbook:

```bash
ansible-playbook -i hosts site.yml
```

Nếu playbook yêu cầu quyền sudo, thêm `-K` hoặc chạy với `--ask-become-pass` theo nhu cầu.

**Tùy chỉnh**
- Biến cấu hình chính nằm trong [group_vars/monitor.yml](group_vars/monitor.yml). Chỉnh các tham số như cổng, volume mount, và enable/disable service.
- Các template Docker Compose nằm trong từng role tại `roles/*/templates/docker-compose.yml.j2` — bạn có thể chỉnh Jinja2 templates nếu cần cấu hình đặc thù.

**Triển khai cụ thể**
- Grafana có cấu hình datasource được đặt sẵn trong [roles/grafana/files/provisioning/datasources/datasources.yml](roles/grafana/files/provisioning/datasources/datasources.yml).
- Prometheus template nằm ở [roles/prometheus/templates/prometheus.yml.j2](roles/prometheus/templates/prometheus.yml.j2).

**Góp ý & Phát triển**
- Mở issue hoặc gửi pull request để bổ sung role, cải thiện templates hoặc thêm tài liệu cấu hình.

**License**
- Tùy theo dự án — thêm file `LICENSE` nếu bạn muốn gắn license rõ ràng.

---
Generated README — chỉnh tiếng Việt hoặc mở rộng nội dung theo môi trường cụ thể của bạn.
