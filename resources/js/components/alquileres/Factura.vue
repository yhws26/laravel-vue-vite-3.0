<script setup>
import { onMounted, ref } from 'vue';
import { useRouter } from 'vue-router';
import { jsPDF } from "jspdf";

let alquiler = ref([])

let form = ref({
    id: '',
    cliente: '',
    vehiculo: '',
    fecha_desde: '',
    fecha_hasta: '',
    precio: '',
    estado: ''
})

onMounted(async () => {
    getFactura()
})

const props = defineProps({
    id: {
        type: String,
        default: ''
    }
})

const getFactura = async () => {

    await axios.get(`/api/get_alquiler/${props.id}`)
        .then(function (response) {
            alquiler.value = response.data.alquiler
            form.value = response.data.alquiler
            console.log('alquiler', alquiler.value)
        })
        .catch((error) => {
            console.log(error.response)
        })
}

const PDF = () => {
    let doc = new jsPDF('1', 'mm', [1550, 650]);

    doc.html(document.getElementById('contenido'), {
        callback: function (doc) {

            //doc.text("Lista Vehiculos", 50, 10);
            doc.save('Factura-Alquiler');
        },
    });
}

</script>

<template>
    <div class="wrapper">
        
        <div class="invoice p-3 mb-3">
            <router-link to="/ListaAlquilados" class="btn btn-primary" style="margin-left: 20px; margin-bottom: 20px;"><i class="fas fa-arrow-left"></i></router-link>

            <div class="row mb-2" style="margin-left:-100px; padding-bottom: 10px; margin-top: 10px;">

                <div class="col-sm-1">
                    <img src="../../../../public/favicon.ico" alt="AdminLTE Logo" class="brand-image img-circle elevation-2"
                        style="opacity: .8; height: 80px; width: 80px;">
                </div><!-- /.col -->
                <div class="col-sm-6">
                    RentsUs - Alquiler de Vehiculos<br>
                    Some place in the World maybe behind iu<br>
                    Contacto: +0412-nocap
                </div><!-- /.col -->

                <div class="row invoice-info" style="margin-top: 50px; margin-left: 100px;">
                    <div class="col-sm-3 invoice-col">
                    De:
                    <address>
                    <strong>RentsUs C.A.</strong><br>
                    En las afueras de la ujap <br>
                    Cera, CA 125345<br>
                    Tlf: (804) 123-5432<br>
                    Email: info@almasaeedstudio.com
                    </address>
                    </div>
                    <div class="col-sm-3 invoice-col">
                    Para:
                    <address>
                    <strong>Cliente 001</strong><br>
                    795 Folsom Ave, Suite 600<br>
                    San Francisco, CA 94107<br>
                    Tlf: (555) 539-1037<br>
                    Email: john.doe@example.com
                    </address>
                    </div>
                    <div class="col-sm-2 invoice-col">
                    <b>Factura #007612</b><br>
                    <br>
                    <b>Orden ID:</b> 4F3S8J<br>
                    <b>Emision:</b> 2/22/2014<br>
                    <b>cuenta:</b> 968-34567
                    </div>
                </div>
            </div>
            
            <div class="row">

            <div class="col-xs-6" style="margin-left: 400px;  margin-top: 50px;">
            <p class="lead">Fecha Emitida 13/01/2023</p>
            </div>

            </div>

            <div class="col-8" style="margin-left: 200px; margin-top: 30px;">

                <div class="card-body">
                    <table class="table table-striped" style=" height: 300px;">
                        <thead>
                            <tr>
                                <th>N°</th>
                                <th>Vehiculo</th>
                                <th>Desde</th>
                                <th>Hasta</th>
                                <th>Precio</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>{{ alquiler.id }}</td>
                                <td>{{ alquiler.vehiculo }}</td>
                                <td>{{ alquiler.fecha_desde }}</td>
                                <td>{{ alquiler.fecha_desde }}</td>
                                <td>{{ alquiler.precio }}</td>
                            </tr>
                        </tbody>
                    </table>
                </div>

            </div>
            <div class="row" style="margin-top:100px;">
                <div class="col-7">
                    <div class="table">
                        <table class="table">
                            <tbody>
                                <tr>
                                    <th style="width:70%">Subtotal:</th>
                                    <td>{{ alquiler.precio-(alquiler.precio * 0.16)}} $</td>
                                </tr>
                                <tr>
                                    <th>IVA (16%)</th>
                                    <td>{{ alquiler.precio * 0.16 }} $</td>
                                </tr>
                                <tr>
                                    <th>Total:</th>
                                    <td>{{ alquiler.precio }} $</td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
            <div class="row no-print">
                <div class="col-11" style="margin-top: 30px;">
                    <a onclick="window.print()" rel="noopener" target="_blank" class="btn btn-primary" style="margin-left: 200px;"><i
                            class="fas fa-print"></i> Imprimir</a>
                </div>
            </div>
        </div>

        <!-- Main Footer -->
        <footer class="main-footer" style="margin-top: 600px;">
            <div class="float-right d-none d-sm-inline">Alquiler de Vehiculos | RentsUs</div>
            <strong>Copyright &copy; 2023 <a href="https://youtu.be/UTH1VNHLjng">RentsUs</a>.</strong> Todoslos derechos reservados.
            <img src="../.../../../../../public/img/adobecolor.png" class="" style="width: 150px; margin-left: 100px;">
        </footer>
    </div>
</template>
