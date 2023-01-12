<script setup>
import { onMounted, ref } from 'vue';
import { useRouter } from 'vue-router'

let form = ref({
    id: '',
    marca: '',
    modelo: '',
    placa: '',
    color: '',
    tipo: '',
    estado: '',
    foto_primera: '',
    foto_segunda: '',
    foto_tercera: ''
})
let tipos = ref([])

onMounted(async () => {
    get_vehiculo();
    //getTipos();
})


const usuarioId = JSON.parse(document.head.querySelector('meta[name="user"]').content);

const router = useRouter();


const onPerfil = () => {
    router.push('/perfil/' + usuarioId);
}

const props = defineProps({
    id: {
        type: String,
        default: ''
    }
})

const get_vehiculo = async () => {
    let response = await axios.get(`/api/get_vehiculo/${props.id}`)
    form.value = response.data.vehiculo
}

const getTipos = async () => {

    let response = await axios.get('api/get_tipos')
    tipos.value = response.data.tipos
    //console.log('tipos', tipos.value)
}

const getFotoPrimera = () => {
    let foto_primera = '/upload/image.png';
    if (form.value.foto_primera != null) {
        if (form.value.foto_primera.indexOf('base64') != -1) {
            foto_primera = form.value.foto_primera;
        } else {
            foto_primera = '/upload/' + form.value.foto_primera;
        }
    }
    return foto_primera
}

const getFotoSegunda = () => {
    let foto_segunda = '/upload/image.png';
    if (form.value.foto_segunda != null) {
        if (form.value.foto_segunda.indexOf('base64') != -1) {
            foto_segunda = form.value.foto_segunda;
        } else {
            foto_segunda = '/upload/' + form.value.foto_segunda;
        }
    }
    return foto_segunda
}

const getFotoTercera = () => {
    let foto_tercera = '/upload/image.png';
    if (form.value.foto_tercera != null) {
        if (form.value.foto_tercera.indexOf('base64') != -1) {
            foto_tercera = form.value.foto_tercera;
        } else {
            foto_tercera = '/upload/' + form.value.foto_tercera;
        }
    }
    return foto_tercera
}

const actualizarFotoPrimera = (e) => {
    let file = e.target.files[0];
    let reader = new FileReader();
    let limit = 1024 * 1024 * 2;
    if (file['size'] > limit) {
        return false
    }
    reader.onloadend = (file) => {
        form.value.foto_primera = reader.result;
    }

    reader.readAsDataURL(file);
}

const actualizarFotoSegunda = (e) => {
    let file = e.target.files[0];
    let reader = new FileReader();
    let limit = 1024 * 1024 * 2;
    if (file['size'] > limit) {
        return false
    }
    reader.onloadend = (file) => {
        form.value.foto_segunda = reader.result;
    }
    reader.readAsDataURL(file);
}

const actualizarFotoTercera = (e) => {
    let file = e.target.files[0];
    let reader = new FileReader();
    let limit = 1024 * 1024 * 2;
    if (file['size'] > limit) {
        return false
    }
    reader.onloadend = (file) => {
        form.value.foto_tercera = reader.result;
    }

    reader.readAsDataURL(file);
}


const editarVehiculo = () => {
    const formData = new FormData();
    formData.append('marca', form.value.marca);
    formData.append('modelo', form.value.modelo);
    formData.append('placa', form.value.placa);
    formData.append('color', form.value.color);
    formData.append('tipo', form.value.tipo);
    formData.append('estado', form.value.estado);
    formData.append('foto_primera', form.value.foto_primera);
    formData.append('foto_segunda', form.value.foto_segunda);
    formData.append('foto_tercera', form.value.foto_tercera);

    axios.post(`/api/editar_vehiculo/${form.value.id}`, formData)
        .then((response) => {

            form.value.marca = '',
                form.value.modelo = '',
                form.value.placa = '',
                form.value.color = '',
                form.value.tipo = '',
                form.value.estado = '',
                form.value.foto_primera = '',
                form.value.foto_segunda = '',
                form.value.foto_tercera = '',
                router.push('/listaVehiculos')
        })
        .catch((error) => {
            console.log(error.response)
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

        <!-- Content Wrapper. Contains page content -->
        <div class="content-wrapper">
            <!-- Content Header (Page header) -->
            <div class="content-header">
                <div class="container-fluid">
                    <div class="row mb-2">
                        <div class="col-sm-6">
                        </div><!-- /.col -->
                    </div><!-- /.row -->
                </div><!-- /.container-fluid -->
            </div>
            <!-- /.content-header -->
            <!-- Main content -->
            <div class="content">
                <div class="container-fluid">
                    <div class="row">
                        <!-- /.col-md-6 -->
                        <div class="col-lg-6">
                            <div class="card border-danger text-danger">
                                <div class="card-header">
                                    <h3 class="card-title">Fotos</h3>
                                </div>
                                <!-- /.card-header -->
                                <!-- form start -->
                                <form>
                                    <div class="card-body">
                                        <div class="form-group">
                                            <div class="container" style="border: 1px solid red">
                                                <div class="mySlides" style="text-align: center;">
                                                    <div class="numbertext text-dark font-weight-bold fs-5">1 / 3</div>
                                                    <img :src="getFotoPrimera()" style="width:350px; height: 350px; object-fit: cover;" alt="primera.png">
                                                </div>

                                                <div class="mySlides" style="text-align: center;">
                                                    <div class="numbertext text-dark font-weight-bold fs-5">2 / 3</div>
                                                    <img :src="getFotoSegunda()" style="width:350px; height: 350px; object-fit: cover; " alt="segunda.png">
                                                </div>

                                                <div class="mySlides" style="text-align: center;">
                                                    <div class="numbertext text-dark font-weight-bold fs-5">3 / 3</div>
                                                    <img :src="getFotoTercera()" style="width:350px; height: 350px; object-fit: cover;" alt="tercera.png">
                                                </div>


                                                <a class="prev bg-danger" onclick="plusSlides(-1)" style="text-decoration: none;">❮</a>
                                                <a class="next bg-danger" onclick="plusSlides(1)" style="text-decoration: none;">❯</a>
                                                

                                                <div class="row" style="">
                                                    <div class="col" style="text-align: center; width:100px; height: 100px; border: 1px solid red;">
                                                        <img :src="getFotoPrimera()" class="demo cursor" style="width:100px; height: 100px; object-fit: cover;"
                                                            onclick="currentSlide(1)" alt="toyota corola">
                                                    </div>
                                                    <div class="col" style="text-align: center; width:100px; height: 100px; border: 1px solid red">
                                                        <img :src="getFotoSegunda()" class="demo cursor" style="width:100px; height: 100px; object-fit: cover;"
                                                            onclick="currentSlide(2)" alt="Carsus">
                                                    </div>
                                                    <div class="col" style="text-align: center; width:100px; height: 100px; border: 1px solid red">
                                                        <img :src="getFotoTercera()" class="demo cursor" style="width:100px; height: 100px; object-fit: cover;"
                                                            onclick="currentSlide(3)" alt="Alquilado">
                                                    </div>
                                                </div>
                                            </div>
                                            <div class="input-group">
                                                <div class="custom-file">
                                                    <input @change="actualizarFotoPrimera" type="file" class="form-control form-control-sm" id="exampleInputFile">
                                                    <label class="custom-file-label" for="exampleInputFile">Elige un
                                                        archivo</label>
                                                </div>
                                                <div class="custom-file">
                                                    <input @change="actualizarFotoSegunda" type="file" class="custom-file-input" id="exampleInputFile">
                                                    <label class="custom-file-label" for="exampleInputFile">Elige un
                                                        archivo</label>
                                                </div>
                                                <div class="custom-file">
                                                    <input @change="actualizarFotoTercera" type="file" class="custom-file-input" id="exampleInputFile">
                                                    <label class="custom-file-label" for="exampleInputFile">Elige un
                                                        archivo</label>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </form>
                            </div>
                        </div>

                        <div class="col-lg-6">
                            <div class="card border-danger">
                                <div class="card-header">
                                    <h3 class="card-title">Formulario</h3>
                                </div>
                                <!-- form -->
                                <form>
                                    <div class="card-body">
                                        <div class="form-group row">
                                            <label for="exampleInputEmail1" class="col-sm-2 col-form-label">Marca</label>
                                            <div class="col-sm-10">
                                                <input type="text" class="form-control" id="exampleInputEmail1"
                                                placeholder="" v-model="form.marca">
                                            </div>
                                        </div>
                                        <div class="form-group row">
                                            <label for="exampleInputEmail1" class="col-sm-2 col-form-label">Modelo</label>
                                            <div class="col-sm-10">
                                                <input type="text" class="form-control" id="exampleInputEmail1"
                                                placeholder="" v-model="form.modelo">
                                            </div>
                                        </div>
                                        <div class="form-group row">
                                            <label for="exampleInputEmail1" class="col-sm-2 col-form-label">Tipo</label>
                                            <div class="col-sm-10">
                                                <div class="input-group" >
                                                <select class="custom-select"  v-model="form.tipo">
                                                    <option>Familiar</option>
                                                    <option>Sport</option>
                                                    <option>Carga</option>
                                                    <option>4x4</option>
                                                    <option v-for="tipo in tipos" :key="tipo.id">{{ tipo.nombre }}</option>
                                                </select>
                                                <div class="input-group-append">
                                                    <router-link to="/tipos" class="btn btn-primary">Registrar Tipos</router-link>
                                                </div>
                                            </div>
                                            </div>
                                        </div>
                                        <div class="form-group row">
                                            <label for="exampleInputEmail1" class="col-sm-2 col-form-label">Placa</label>
                                            <div class="col-sm-10">
                                                <input type="number" class="form-control" id="exampleInputEmail1"
                                                placeholder="" v-model="form.placa">
                                            </div>
                                        </div>
                                        <div class="form-group row">
                                            <label for="exampleInputEmail1" class="col-sm-2 col-form-label">Color</label>
                                            <div class="col-sm-10">
                                                <input type="text" class="form-control" id="exampleInputEmail1"
                                                placeholder="" v-model="form.color">
                                            </div>
                                        </div>
                                        <div class="form-group row">
                                            <label for="exampleInputEmail1" class="col-sm-2 col-form-label">Año</label>
                                            <div class="col-sm-10">
                                                <input type="number" class="form-control" id="exampleInputEmail1"
                                                placeholder="">
                                            </div>
                                        </div>
                                        <div class="form-group row">
                                            <label for="exampleInputEmail1" class="col-sm-2 col-form-label">Estado</label>
                                            <div class="col-sm-10">
                                                <select class="custom-select" v-model="form.estado">
                                                <option>Activo</option>
                                                <option>Inactivo</option>
                                                <option>Alquilado</option>
                                            </select>
                                            </div>
                                        </div>
                                    </div>
                                    <!-- /.card-body -->
                                    <div class="card-footer">
                                        <button type="button" class="btn btn-primary btn-block"
                                            @click="registrarVehiculo()">Guardar</button>
                                    </div>
                                </form>
                            </div>
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

<style>
img {
    vertical-align: middle;
}

/* Position the image container (needed to position the left and right arrows) */
.container {
    position: relative;
    margin-bottom: 10px;
}

/* Hide the images by default */
.mySlides {
    display: none;
    height: 450px;
}

/* Add a pointer when hovering over the thumbnail images */
.cursor {
    cursor: pointer;
}

/* Next & previous buttons */
.prev,
.next {
    cursor: pointer;
    position: absolute;
    top: 40%;
    width: auto;
    padding: 16px;
    margin-top: -50px;
    color: white;
    font-weight: bold;
    font-size: 20px;
    border-radius: 0 3px 3px 0;
    user-select: none;
    -webkit-user-select: none;
    z-index: 5;
}

/* Position the "next button" to the right */
.next {
    right: 0;
    border-radius: 3px 0 0 3px;
    z-index: 5;
}

/* On hover, add a black background color with a little bit see-through */
.prev:hover,
.next:hover {
    background-color: rgba(0, 0, 0, 0.8);
}

/* Number text (1/3 etc) */
.numbertext {
    color: #f2f2f2;
    font-size: 12px;
    padding: 8px 12px;
    position: absolute;
    top: 0;
}

/* Container for image text */
.caption-container {
    text-align: center;
    background-color: #222;
    padding: 2px 16px;
    color: white;
}

.row {
    display: flex;
    justify-content: center;
}

.row:after {
    content: "";
    display: table;
    clear: both;
}

/* Six columns side by side */
.column {
    float: left;
    width: 16.66%;
}

/* Add a transparency effect for thumnbail images */
.demo {
    opacity: 0.6;
}

.active,
.demo:hover {
    opacity: 1;
}
</style>
