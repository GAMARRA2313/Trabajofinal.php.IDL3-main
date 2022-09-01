<template>
  <div>
		<h1 class="text-center">Lista de Tipos</h1>

		<div class="text-center">
			<b-button variant="primary" @click="Nuevo">Agregar</b-button>
		</div>
		<br />
		<div class="col-md-8 offset-md-2">
			<b-table
				:sticky-header="true"
				:no-border-collapse="false"
				responsive
				:items="lista_tipos"
				:fields="columnas"
			>
				
				<template #head()="scope">
					<div class="text-nowrap">{{ scope.label }}</div>
				</template>

				<template #cell(N°)="data">
					{{ data.index + 1 }}
				</template>
				<template #cell(habilitado)="data">
					{{ data.item.habilitado ? "SI" : "NO" }}
				</template>

				<template #cell(acciones)="{ item }">
					<b-button-group>
						<b-btn variant="warning" @click="Editar(item)">Editar</b-btn>
						<b-btn variant="danger" @click="Eliminar(item)">Eliminar</b-btn>
					</b-button-group>
				</template>
			</b-table>
		</div>

		<mdlDatosTipo ref="mdlDatosTipo"></mdlDatosTipo>
	</div>
  </template>
<script>
import axios from "axios";
import mdlDatosTipo from "./components/mdlDatosTipo.vue";
export default {
	components: { mdlDatosTipo },
	props: [],
	data() {
		return {
			lista_tipos: [],
			columnas: [
				"N°",
				{ key: "tipo", label: "Tipo" },
				{ key: "descripcion", label: "Descripción" },
				{ key: "habilitado", label: "Habilitado" },
				{ key: "created_at", label: "Creado" },
				{ key: "updated_at", label: "Actualizado" },
				"acciones",
			],
		};
	},
	computed: {},
	mounted() {
		this.ListarTipos();
	},
	methods: {
		async ListarTipos() {
			let self = this;
			await axios
				.get("http://laravel.test/tipos")
				.then(function (response) {
					self.lista_tipos = response.data.data;
				});
		},
		Nuevo() {
			let mdlDatosTipo = this.$refs.mdlDatosSector;
			mdlDatosTipo.frmDatosTipo.tipo = null;
			mdlDatosTipo.frmDatosTipo.descripcion = null;
			mdlDatosTipo.frmDatosTipo.habilitado = 1;
			mdlDatosTipo.modo = "NUEVO";
			mdlDatosTipo.Abrir();
		},
		Editar(item) {
			let mdlDatosTipo = this.$refs.mdlDatosTipo;
			mdlDatosTipo.frmDatosTipo.id = item.id;
			mdlDatosTipo.frmDatosTipo.tipo = item.tipo;
			mdlDatosTipo.frmDatosTipo.descripcion = item.descripcion;
			mdlDatosTipo.frmDatosTipo.habilitado = item.habilitado;
			mdlDatosTipo.modo = "EDITAR";
			mdlDatosTipo.Abrir();
		},
		Eliminar(item) {
			let self = this;
			this.$swal
				.fire({
					icon: "question",
					text: "¿DESEA ELIMINAR ESTE SECTOR?",
					confirmButtonText: "Si",
					showCancelButton: true,
					cancelButtonText: "No",
					allowOutsideClick: false,
				})
				.then((result) => {
					if (result.isConfirmed) {
						let sector_id = item.id;
						axios
							.delete("http://laravel.test/api/sectores/" + sector_id)
							.then(function (response) {
								let status = response.status;
								if (status == 200) {
									self.ListarSectores();
									return self.$swal.fire({
										icon: "success",
										title: "¡ÉXITO!",
										text: "El registro se eliminó correctamente.",
									});
								}
							});
					}
				});
		},
	},
};
</script>    
  