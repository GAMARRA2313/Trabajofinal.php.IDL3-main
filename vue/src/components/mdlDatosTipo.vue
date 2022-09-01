<template>
	<!-- Modal -->
	<b-modal ref="mdlDatosTipo" hide-footer :title="titulo">
		<div class="d-block">
			<b-form>
				<b-form-group label="Tipo">
					<b-form-input
						v-model="frmDatosTipo.tipo"
						type="text"
						placeholder="Ingrese el tipo"
						required
					></b-form-input>
				</b-form-group>
				<b-form-textarea
					v-model="frmDatosTipo.descripcion"
					placeholder="Descripción..."
					rows="3"
					max-rows="6"
				></b-form-textarea>
				<b-form-checkbox
					v-model="frmDatosTipo.habilitado"
					v-if="modo == 'EDITAR'"
					value="1"
					unchecked-value="0"
				>
					Habilitado
				</b-form-checkbox>
			</b-form>
		</div>
		<b-button
			class="mt-3"
			variant="outline-success"
			block
			@click="Guardar"
			v-if="modo == 'NUEVO'"
			>Guardar</b-button
		>
		<b-button
			class="mt-3"
			variant="outline-success"
			block
			@click="Actualizar"
			v-if="modo == 'EDITAR'"
			>Actualizar</b-button
		>

		<b-button class="mt-3" variant="outline-danger" block @click="Cerrar"
			>Cerrar</b-button
		>
	</b-modal>
</template>

<script>
import axios from "axios";
export default {
	props: {},
	data() {
		return {
			modo: null,
			frmDatosTipo: {
				id: null,
				Tipo: null,
				descripcion: null,
				habilitado: 0,
			},
		};
	},
	computed: {
		titulo() {
			let valor = null;
			if (this.modo == "NUEVO") {
				valor = "Nuevo Tipo";
			} else if (this.modo == "Editar") {
				valor = "Editar Tipo";
			}
			return valor;
		},
	},
	methods: {
		Abrir() {
			this.$refs["mdlDatosTipo"].show();
		},
		Cerrar() {
			this.$refs["mdlDatosTipo"].hide();
		},
		Guardar() {
			let self = this;
			if (this.frmDatosTipo.tipo == null) {
				this.$swal.fire({
					icon: "error",
					title: "¡Ups!",
					text: "El TIPO no puede quedar vacío.",
				});
				return false;
			}
			this.$swal
				.fire({
					icon: "question",
					text: "¿DESEA REGISTRAR ESTE TIPO?",
					confirmButtonText: "Si",
					showCancelButton: true,
					cancelButtonText: "No",
					allowOutsideClick: false,
				})
				.then(async (result) => {
					if (result.isConfirmed) {
						let data = new FormData();
						data.append("tipo", self.frmDatosTipo.tipo);
						data.append("descripcion", self.frmDatosTipo.descripcion);
						await axios
							.post("http://laravel.test/api/sectores", data)
							.then(function (response) {
								let status = response.status;
								if (status == 200) {
									self.$parent.ListarTipos();
									self.Cerrar();
									return self.$swal.fire({
										icon: "success",
										title: "¡ÉXITO!",
										text: "El registro se agregó correctamente.",
									});
								}
							});
					}
				});
		},
		Actualizar() {
			let self = this;
			if (this.frmDatosTipo.tipo == null) {
				this.$swal.fire({
					icon: "error",
					title: "¡Ups!",
					text: "El TIPO no puede quedar vacío.",
				});
				return false;
			}
			this.$swal
				.fire({
					icon: "question",
					text: "¿DESEA GUARDAR LOS CAMBIOS?",
					confirmButtonText: "Si",
					showCancelButton: true,
					cancelButtonText: "No",
					allowOutsideClick: false,
				})
				.then((result) => {
					if (result.isConfirmed) {
						let tipo_id = self.frmDatosTipo.id;
						let data = {
							tipo: self.frmDatosTipo.tipo,
							descripcion: self.frmDatosTipo.descripcion,
							habilitado: self.frmDatosTipo.habilitado,
						};
						axios
							.put("http://laravel.test/api/sectores/" + tipo_id, data)
							.then(function (response) {
								let status = response.status;
								if (status == 200) {
									self.$parent.ListarTipos();
									self.Cerrar();
									return self.$swal.fire({
										icon: "success",
										title: "¡ÉXITO!",
										text: "El registro se actualizó correctamente.",
									});
								}
							});
					}
				});
		},
	},
};
</script>

<style class="css">
/* #mdlDatosTipo {
	top: 20%;
} */
</style>
