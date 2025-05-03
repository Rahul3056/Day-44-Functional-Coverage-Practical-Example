### **Day 44: Functional Coverage – Practical Example**

---

### **Objective:**

Today we will apply what we learned about functional coverage by building a **complete practical example** that:

* Uses **randomization**
* Defines **covergroups**, **coverpoints**, and **cross coverage**
* Demonstrates **coverage sampling**
* Represents a simplified **real-world verification scenario**

---

### **Scenario:**

We are verifying a **simple packet-based protocol**, where each packet contains:

* A `type` (0 = control, 1 = data, 2 = error)
* A `size` (packet size from 0 to 15)
* A `priority` (low, medium, high)

We want to ensure that:

1. All packet types are exercised.
2. Packet sizes are tested across defined ranges.
3. All combinations of type and priority are tested.

---

### **Step 1: Define the Class with Covergroup**

```systemverilog
class Packet;

    rand bit [3:0] size;
    rand bit [1:0] type;     // 0 = control, 1 = data, 2 = error
    rand bit [1:0] priority; // 0 = low, 1 = medium, 2 = high

    // Covergroup declaration
    covergroup packet_cg;
        coverpoint type {
            bins control = {0};
            bins data    = {1};
            bins error   = {2};
        }

        coverpoint size {
            bins small  = {[0:5]};
            bins medium = {[6:10]};
            bins large  = {[11:15]};
        }

        coverpoint priority {
            bins low    = {0};
            bins medium = {1};
            bins high   = {2};
        }

        // Cross coverage for type and priority
        cross type, priority;
    endgroup

    // Constructor
    function new();
        packet_cg = new();
    endfunction

    // Sampling function
    function void sample();
        packet_cg.sample();
    endfunction

endclass
```

---

### **Step 2: Randomize and Sample in Testbench**

```systemverilog
module tb;

    Packet pkt;

    initial begin
        pkt = new();

        // Generate and sample 50 packets
        repeat (50) begin
            if (pkt.randomize()) begin
                $display("Packet => Type: %0d, Size: %0d, Priority: %0d",
                          pkt.type, pkt.size, pkt.priority);
                pkt.sample();
            end else begin
                $display("Randomization failed.");
            end
        end

        $display("Simulation complete. Analyze functional coverage.");
    end

endmodule
```

---

### **Expected Outcome:**

* The `packet_cg` covergroup will track:

  * Which `type` values occurred
  * Which `size` bins were hit
  * Which `priority` levels were used
  * Whether all combinations of `type` and `priority` occurred

* The coverage report (via simulator) will indicate **coverage percentages** and show any **missing bins or combinations**.

---

### **Benefits:**

* This example mimics **real protocol-level testing**.
* Helps ensure **broad functional stimulus** coverage.
* Demonstrates how **coverage data** can guide **testbench improvement**.
