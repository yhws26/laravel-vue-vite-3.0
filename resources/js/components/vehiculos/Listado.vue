<script setup>
import { onMounted, ref } from 'vue';
import { useRouter } from 'vue-router';
import { jsPDF } from "jspdf";

const usuarioId = JSON.parse(document.head.querySelector('meta[name="user"]').content);

const router = useRouter();

const onPerfil = () => {
    router.push('/perfil/'+usuarioId);
}

const editarVehiculo = (id) => {
    router.push('/editarVehiculo/' + id)
}

let vehiculos = ref([])

onMounted(async () => {
    getVehiculos()
})

const getVehiculos = async () => {

    let response = await axios.get('api/get_vehiculos')
    vehiculos.value = response.data.vehiculos
    console.log('vehiculos', vehiculos.value)
}

const PDF = () => {
    let doc = new jsPDF('1', 'mm', [1050, 500]);

    doc.html(document.getElementById('tabla-vehiculos'), {
        callback: function (doc) {

            //doc.text("Lista Vehiculos", 50, 10);
            doc.save('Tabla de Vehiculos');
        },
    });
}

const eliminarVehiculo = (id) => {
    axios.get(`/api/eliminar_vehiculo/${id}`)
        .then(() => {
            getVehiculos()
        })
        .catch(() => {
            console.log()
        })
}

</script>

<template>
    <div class="wrapper">
        <!-- Sidebar Container -->
        <aside class="main-sidebar bg-dark sidebar-dark-primary elevation-4">
            <!-- Logo -->
            <router-link to="/home" class="brand-link">
                <img src="../../../../public/img/AdminLTELogo.png" alt="RentsUsLogo" class="brand-image img-circle elevation-3"
                    style="opacity: .8">
                <span class="brand-text font-weight-light fs-4"><a href="/home" style="text-decoration: none; color: white;">RentsUs</a></span>
            </router-link>
            <!-- Sidebar -->
            <div class="sidebar">
                <!-- Sidebar Menu -->
                <nav class="mt-2">
                    <ul class="nav nav-pills nav-sidebar flex-column" data-widget="treeview" role="menu"
                        data-accordion="false">
                        <li class="nav-item" style="border-radius: 7px">
                            <router-link to="/usuarios" class="nav-link">
                                <i class="fas fa-user-circle" style="padding-top: 10px; padding-bottom: 10px;"></i>
                                <p> Usuarios </p>
                            </router-link>
                        </li>
                        <li class="nav-item" style="border-radius: 7px; margin: 10px 0">
                            <a @click="onPerfil" class="nav-link" style="cursor: pointer;">
                                <i class="fas fa-address-card" style="padding-top: 10px; padding-bottom: 10px"></i>
                                <p> Perfil </p>
                            </a>
                        </li>
                        <li class="nav-item" style="border-radius: 7px; margin-bottom: 10px;">
                            <router-link to="/listaVehiculos" class="nav-link" style="cursor: pointer;">
                                <i class="fas fa-car-side" style="padding-top: 10px; padding-bottom: 10px;"></i>
                                <p> Vehiculos </p>
                            </router-link>
                        </li>
                        <li class="nav-item" style="border-radius: 7px">
                            <router-link to="/ListaAlquilados" class="nav-link" style="cursor: pointer;">
                                <i class="fas fa-car" style="padding-top: 10px; padding-bottom: 10px;"></i>
                                <p>
                                    Vehiculos alquilados
                                </p>
                            </router-link>
                        </li>
                    </ul>
                </nav>
                <!-- /.sidebar-menu -->
            </div>
            <!-- Dropdown de configuracion -->
            <div class="dropdown"  style="position: fixed;margin: 500px 0 0 30px;">
                <a href="#" class="d-flex align-items-center text-white text-decoration-none dropdown-toggle" id="dropdownUser1" data-bs-toggle="dropdown" aria-expanded="false">
                    <img src="../../../../public/img/avatar.png" alt="" width="32" height="32" class="rounded-circle me-2">
                    <strong>Config</strong>
                </a>
                <ul class="dropdown-menu dropdown-menu-dark text-small shadow" aria-labelledby="dropdownUser1">
                    <li><a class="dropdown-item" href="#">Configuraciones</a></li>
                    <li><a class="dropdown-item" href="#">Ubicacion</a></li>
                    <li><hr class="dropdown-divider"></li>
                    <li><a class="dropdown-item" href="#">Soporte</a></li>
                </ul>
            </div>
        </aside>


        <!-- Content Wrapper-->
        <div class="content-wrapper">

            <div class="content-header">
                <div class="container-fluid">
                    <div class="row mb-2">
                        <div class="col-sm-6">
                        </div>
                    </div>
                </div>
            </div>
            <!-- /.content-header -->
            <!-- Main content -->
            <div class="content">
                <div class="container-fluid">
                    <div class="row">
                        <!-- /.col-md-6 -->
                        <div class="col-lg-12">
                            <div class="card border-danger text-danger" style="height: 600px;">
                                <div class="card-header">
                                    <h3 class="card-title">Vehiculos</h3>
                                </div>
                                
                                <div class="card-body">
                                    <!-- pdf -->
                                    <div class="mb-2">
                                        <div class="btn-group">
                                            <button type="button" class="btn btn-danger" style="border-radius: 2px;" @click="PDF">Descargar PDF <i class="fas fa-file"></i></button>
                                        </div>
                                        <!-- Search -->
                                        <div class="card-tools float-right">
                                            <!-- item buscar -->
                                            <div class="input-group input-group-sm" style="width: 150px;">
                                                <input type="text" name="table_search" class="form-control float-right"
                                                    placeholder="Buscar">
                                                <div class="input-group-append">
                                                    <button type="submit" class="btn btn-default">
                                                        <i class="fas fa-search"></i>
                                                    </button>
                                                </div>
                                            </div>
                                        </div>                                        
                                    </div>
                                    
                                    <div id="tabla-vehiculos">
                                        <table class="table table-striped table-dark">
                                            <thead>
                                                <tr>
                                                    <th>Marca</th>
                                                    <th>Modelo</th>
                                                    <th>Placa</th>
                                                    <th>Estado</th>
                                                    <th style="width: 150px;">Opciones</th>
                                                </tr>
                                            </thead>
                                            <tbody v-for="vehiculo in vehiculos" :key="vehiculo.id">
                                                <tr>
                                                    <td>{{ vehiculo.marca }}</td>
                                                    <td>{{ vehiculo.modelo }}</td>
                                                    <td>{{ vehiculo.placa }}</td>
                                                    <td>{{ vehiculo.estado }}</td>
                                                    <td>
                                                        <div class="btn-group">
                                                            <button type="button"
                                                                class="btn btn-danger" @click="eliminarVehiculo(vehiculo.id)">Eliminar</button>
                                                            <button @click="editarVehiculo(vehiculo.id)" type="button"
                                                                class="btn btn-primary">Editar</button>
                                                        </div>
                                                    </td>
                                                </tr>
                                            </tbody>
                                        </table>
                                    </div>
                                </div>
                            </div>
                            <nav aria-label="...">
                                    <ul class="pagination float-right">
                                        <li class="page-item active">
                                        <a class="page-link" href="#" tabindex="-1" aria-disabled="true">Anterior</a>
                                        </li>
                                        <li class="page-item"><a class="page-link" href="#">1</a></li>
                                        <li class="page-item disabled" aria-current="page">
                                        <a class="page-link" href="#">2</a>
                                        </li>
                                        <li class="page-item"><a class="page-link" href="#">3</a></li>
                                        <li class="page-item">
                                        <a class="page-link" href="#">Siguiente</a>
                                        </li>
                                    </ul>
                                </nav>
                                
                                <router-link to="/registrarVehiculo" class="btn btn-primary">Registrar Vehiculo</router-link>
                        </div>
                        <!-- /.col-md-6 -->
                    </div>
                    <!-- /.row -->
                </div><!-- /.container-fluid -->
            </div>
            <!-- /.content -->
        </div>
        <!-- /.content-wrapper -->

        <!-- Control Sidebar -->
        <aside class="control-sidebar control-sidebar-dark">
            <!-- Control sidebar content goes here -->
            <div class="p-3">
                <h5>Title</h5>
                <p>Sidebar content</p>
            </div>
        </aside>
        <!-- /.control-sidebar -->

        <!-- Main Footer -->
        <footer class="main-footer">
            <div class="float-right d-none d-sm-inline">Alquiler de Vehiculos | RentsUs</div>
            <strong>Copyright &copy; 2023 <a href="https://youtu.be/UTH1VNHLjng">RentsUs</a>.</strong> Todoslos derechos reservados.
            <img src="../.../../../../../public/img/adobecolor.png" class="" style="width: 150px; margin-left: 100px;">
        </footer>
    </div>
</template>
