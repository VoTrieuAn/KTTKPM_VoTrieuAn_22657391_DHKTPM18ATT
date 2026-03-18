# Note bài cần làm
Tại link: https://docs.google.com/spreadsheets/d/1bfLdY9mywovqnXP1LbINSrIh7R6LhS9wmFGRtLOgKmw/edit?gid=1099688807#gid=1099688807
Vào Sheet buổi 2 làm bài tập: 2. State, Strategy, Decorator theo các mô tả bên dưới
Yêu cầu: 
- Vẽ UML
- Code Java cho UML đó
--> Lưu ý: Mỗi mô tả đều áp dụng cả 3 Pattern đó sau đó đưa ra kết luận so sánh

# Mô tả 1:
// Strategy cho vận chuyển
interface ShippingStrategy {
    void ship();
}

// Interface cho các Trạng thái
interface OrderState {
    void handleRequest(OrderContext context);
    String getStatusName();
}

class NewOrderState implements OrderState {
    @Override
    public void handleRequest(OrderContext context) {
        System.out.println("-> [Mới tạo]: Đang kiểm tra thông tin đơn hàng...");
        context.setState(new ProcessingState());
    }
    @Override
    public String getStatusName() { return "MỚI TẠO"; }
}

class ProcessingState implements OrderState {
    @Override
    public void handleRequest(OrderContext context) {
        System.out.println("-> [Đang xử lý]: Đóng gói sản phẩm...");
        context.getShippingStrategy().ship(); // Sử dụng Strategy
        context.setState(new DeliveredState());
    }
    @Override
    public String getStatusName() { return "ĐANG XỬ LÝ"; }
}

class DeliveredState implements OrderState {
    @Override
    public void handleRequest(OrderContext context) {
        System.out.println("-> [Đã giao]: Hoàn tất đơn hàng. Cập nhật hệ thống.");
    }
    @Override
    public String getStatusName() { return "ĐÃ GIAO"; }
}

class CancelledState implements OrderState {
    @Override
    public void handleRequest(OrderContext context) {
        System.out.println("-> [Hủy]: Hủy đơn hàng và thực hiện hoàn tiền.");
    }
    @Override
    public String getStatusName() { return "ĐÃ HỦY"; }
}

interface Order {
    double getPrice();
    String getDescription();
}

class BasicOrder implements Order {
    public double getPrice() { return 100.0; }
    public String getDescription() { return "Đơn hàng cơ bản"; }
}

abstract class OrderDecorator implements Order {
    protected Order decoratedOrder;
    public OrderDecorator(Order order) { this.decoratedOrder = order; }
}

class InsuranceDecorator extends OrderDecorator {
    public InsuranceDecorator(Order order) { super(order); }
    public double getPrice() { return decoratedOrder.getPrice() + 15.0; }
    public String getDescription() { return decoratedOrder.getDescription() + " + Bảo hiểm"; }
}

class OrderContext {
    private OrderState state;
    private ShippingStrategy shippingStrategy;

    public OrderContext(ShippingStrategy strategy) {
        this.state = new NewOrderState(); // Mặc định trạng thái đầu
        this.shippingStrategy = strategy;
    }

    public void setState(OrderState state) { this.state = state; }
    public ShippingStrategy getShippingStrategy() { return shippingStrategy; }

    public void apply() {
        state.handleRequest(this);
    }
}

Mô tả 2:
