# SAMG55_120M_internal_regulator_trim
2018/5/1
//当主频跑在120M时，内部regulator是需要通过读取Flash 的唯一识别数据来校准的，因为默认是校准100M的
#if SAMG55
	/* Set the trim value when system run near 120M */
	if ((SystemCoreClock <= (CHIP_FREQ_CPU_MAX + (CHIP_FREQ_CPU_MAX >> 3))) &&
	(SystemCoreClock >= (CHIP_FREQ_CPU_MAX - (CHIP_FREQ_CPU_MAX >> 3)))) {
		/* Get the trim value from unique ID area */
		efc_perform_read_sequence(EFC, EFC_FCMD_STUI, EFC_FCMD_SPUI,
		unique_id, 32);

		trim_value = unique_id[16] & 0x0000FFFF;
		supc_set_regulator_trim_user(SUPC, trim_value);
	}
#endif

#if (defined CONFIG_SYSCLK_DEFAULT_RETURNS_SLOW_OSC)
	/* Signal that the internal frequencies are setup */
	sysclk_initialized = 1;
#endif
}
