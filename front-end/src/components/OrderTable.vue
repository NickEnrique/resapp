<template>
    <div class="container-fluid">
        <div class="row text-center mt-4">
            <div class="col">
                <h2><strong>Hello, Kitchen Staff</strong></h2>
            </div>
        </div>

        <div class="container mt-2 p-2 tablecontainer">
            <div class="row text-center">
                <div class="col">
                    <h4>List of Orders</h4>
                </div>
            </div>

            <div v-if="displayPopup" class="mymodal">
                <div class="modal-content">
                    <div class="row text-center">
                        <div class="col"><h2>Edit Order Status</h2></div>
                    </div>
                    <form>
                        <div class="form-group">
                            <label for="orderID">OrderID</label>
                            <input type="text" id="orderID" :value="updatingOrder.OrderID" class="form-control" disabled>
                        </div>
                        <div class="form-group">
                            <label for="timestamp">Timestamp</label>
                            <input type="text" id="timestamp" :value="updatingOrder.Timestamp" class="form-control" disabled>
                        </div>
                        <div class="form-group">
                            <label for="tableID">TableID</label>
                            <input type="text" id="tableID" :value="updatingOrder.TableID" class="form-control" disabled>
                        </div>
                        <div class="form-group">
                            <label for="items">Ordered Items</label>
                            <textarea id="items" class="form-control" :value="updatingOrder.Items" disabled></textarea>
                        </div>
                        <div class="form-group">
                            <label for="orderStatus">Order Status</label>
                            <select id="orderStatus" v-model="updatingOrder.OrderStatus" class="form-control">
                                <option value="Pending">Pending</option>
                                <option value="Completed">Completed</option>
                            </select>
                        </div>
                        <div class="form-group text-center">
                            <button type="button" @click="saveOrderStatus" class="btn btn-primary">Update</button>
                            <button type="button" @click="closeUpdatePopup" class="btn btn-danger">Close</button>
                        </div>
                    </form>
                </div>
            </div>

            <div id="table-responsive">
                <table class="table table-striped table-hover text-center">
                    <thead>
                        <tr>
                            <th>OrderID</th>
                            <th>Timestamp</th>
                            <th>TableID</th>
                            <th class="col-5">Items</th>
                            <th>Order Status</th>
                            <th>Update</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="order in orderList" :key="order.OrderID">
                            <td>{{order.OrderID}}</td>
                            <td>{{order.Timestamp}}</td>
                            <td>{{order.TableID}}</td>
                            <td>{{order.Items}}</td>
                            <td>{{order.OrderStatus}}</td>
                            <td><a href="#" @click="updateOrder(order); openUpdatePopup()">Update</a></td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

    export default{
        name: 'OrderTable',
        data(){
            return{
                orderList: [],
                displayPopup: false,
                updatingOrder: []
            }
        },
        methods:{
            updateOrder(order){
                this.updatingOrder = order;
            },
            openUpdatePopup(){
                this.displayPopup = true;
            },
            closeUpdatePopup(){
                this.displayPopup = false;
                this.updatingOrder = [];
            },
            saveOrderStatus() {
                axios.put(`http://localhost:3000/orders/${this.updatingOrder.OrderID}`, {
                    OrderStatus: this.updatingOrder.OrderStatus
                })
                .then(() => {
                    this.closeUpdatePopup();
                    this.fetchOrders(); // Fetch orders again to update the table without reloading the page
                })
                .catch(error => {
                    console.error('There was an error updating the order status:', error);
                });
            },
            fetchOrders() {
                axios.get('http://localhost:3000/orders')
                .then(response => {
                    this.orderList = response.data;
                })
                .catch(error => {
                    console.error('There was an error fetching the orders:', error);
                });

                //Add functions to clean up the data especially for Timestamp and Items
            }
        },
        created(){
            this.fetchOrders();
        }
    }
</script>

<style scoped>
    #table-responsive{
        overflow-y: scroll;
        height: 400px;
    }
    .tablecontainer{
        position: relative;
        max-width: 85%; 
        background-color: lightgrey;
        border: 1px solid black;
        border-radius: 5px;
    }
    thead {
        position: sticky;
        top: 0;
        background-color: white;

    }

    /* The Modal (background) */
    .mymodal {
        position: fixed;
        z-index: 1;
        left: 0;
        top: 0;
        width: 100%;
        height: 100%;
        overflow: auto;
        background-color: rgba(0, 0, 0, 0.4);
        display: flex;
        align-items: center;
        justify-content: center;
    }

    /* Modal Content/Box */
    .modal-content {
        background-color: #fefefe;
        margin: auto;
        padding: 20px;
        border: 1px solid #888;
        width: 70%;
        max-width: 800px;
        border-radius: 8px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
    }

    .form-group {
        margin-bottom: 1rem;
    }

    .form-control {
        width: 100%;
        padding: 0.5rem;
        margin: 0.5rem 0;
    }

    .btn {
        margin: 0.5rem;
    }
</style>