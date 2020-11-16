 # Вывод HIGHLOAD блока, привязанному к разделу 
/**
 * @var array $problems
 */
if (Loader::includeModule('iblock') && Loader::includeModule('highloadblock')) {
	$db_list = CIBlockSection::GetList(
		array("ID" => "ASC"),
		array("IBLOCK_ID" => "1", "ID" => $arResult["VARIABLES"]["SECTION_ID"]),
		false,
		array("ID", "IBLOCK_ID", "UF_PROBLEMS")
	);
	if ($db_el = $db_list->GetNext()) {
		$hlblock = HighloadBlockTable::getById($arParams["PROBLEMS_HIGHLOAD_ID"])->fetch();
		$hlentity = HighloadBlockTable::compileEntity($hlblock);
		$hldataclass = $hlentity->getDataClass();

		$hl_db_list = $hldataclass::getList(array(
			'select' => array('*'),
			'filter' => array(
				'ID' => $db_el['UF_PROBLEMS']
			),
		));
		while ($hl_db_el = $hl_db_list->Fetch()) {
			$problems[] = $hl_db_el;
		}
	}
}

