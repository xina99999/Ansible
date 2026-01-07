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

2. Add SSH key vào server cần chạy

Trước tiên, đảm bảo  đã có SSH key (ví dụ: `id_rsa.pub` hoặc `rsa.pub`) trên máy local.

 Bước 1: Copy SSH public key lên server

Chạy lệnh sau trên **máy local**:

```bash
scp ~/.ssh/id_rsa.pub user@<SERVER_IP>:/tmp/id_rsa.pub
```

Ví dụ:

```bash
scp ~/.ssh/id_rsa.pub ubuntu@192.168.1.10:/tmp/id_rsa.pub
```

Nhập mật khẩu SSH của user trên server khi được yêu cầu.

---

Bước 2: Add key vào `authorized_keys` trên server

SSH vào server:

```bash
ssh user@<SERVER_IP>
```

Sau đó chạy:

```bash
mkdir -p ~/.ssh
cat /tmp/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

Bước 3: Kiểm tra đăng nhập bằng SSH key

Thoát khỏi server và thử đăng nhập lại:

```bash
ssh user@<SERVER_IP>
```

Nếu không bị hỏi mật khẩu → SSH key đã được cấu hình thành công 

---

3. Chạy test connect với server:
```bash
ansible <server> -i hots -m ping
```
Nếu chạy thành công nghĩa là connect kết nối được
4. Chạy playbook:
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


