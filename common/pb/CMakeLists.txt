# CMakeLists for common directory
#
# provides the protobuf interfaces

set(PROTO_FILES
    admin_commands.proto
    card_attributes.proto
    color.proto
    command_attach_card.proto
    command_change_zone_properties.proto
    command_concede.proto
    command_create_arrow.proto
    command_create_counter.proto
    command_create_token.proto
    command_deck_del_dir.proto
    command_deck_del.proto
    command_deck_download.proto
    command_deck_list.proto
    command_deck_new_dir.proto
    command_deck_select.proto
    command_deck_upload.proto
    command_del_counter.proto
    command_delete_arrow.proto
    command_draw_cards.proto
    command_dump_zone.proto
    command_flip_card.proto
    command_game_say.proto
    command_inc_card_counter.proto
    command_inc_counter.proto
    command_kick_from_game.proto
    command_leave_game.proto
    command_move_card.proto
    command_mulligan.proto
    command_next_turn.proto
    command_ready_start.proto
    command_replay_delete_match.proto
    command_replay_list.proto
    command_replay_download.proto
    command_replay_modify_match.proto
    command_reveal_cards.proto
    command_reverse_turn.proto
    command_roll_die.proto
    command_set_active_phase.proto
    command_set_card_attr.proto
    command_set_card_counter.proto
    command_set_counter.proto
    command_set_sideboard_plan.proto
    command_set_sideboard_lock.proto
    command_shuffle.proto
    commands.proto
    command_undo_draw.proto
    context_concede.proto
    context_connection_state_changed.proto
    context_deck_select.proto
    context_move_card.proto
    context_mulligan.proto
    context_ping_changed.proto
    context_ready_start.proto
    context_set_sideboard_lock.proto
    context_undo_draw.proto
    event_add_to_list.proto
    event_attach_card.proto
    event_change_zone_properties.proto
    event_connection_closed.proto
    event_create_arrow.proto
    event_create_counter.proto
    event_create_token.proto
    event_del_counter.proto
    event_delete_arrow.proto
    event_destroy_card.proto
    event_draw_cards.proto
    event_dump_zone.proto
    event_flip_card.proto
    event_game_closed.proto
    event_game_host_changed.proto
    event_game_joined.proto
    event_game_say.proto
    event_game_state_changed.proto
    event_join.proto
    event_join_room.proto
    event_kicked.proto
    event_leave.proto
    event_leave_room.proto
    event_list_games.proto
    event_list_rooms.proto
    event_move_card.proto
    event_player_properties_changed.proto
    event_remove_from_list.proto
    event_replay_added.proto
    event_reveal_cards.proto
    event_reverse_turn.proto
    event_roll_die.proto
    event_room_say.proto
    event_remove_messages.proto
    event_server_complete_list.proto
    event_server_identification.proto
    event_server_message.proto
    event_server_shutdown.proto
    event_set_active_phase.proto
    event_set_active_player.proto
    event_set_card_attr.proto
    event_set_card_counter.proto
    event_set_counter.proto
    event_shuffle.proto
    event_user_joined.proto
    event_user_left.proto
    event_user_message.proto
    event_notify_user.proto
    game_commands.proto
    game_event_container.proto
    game_event_context.proto
    game_event.proto
    game_replay.proto
    isl_message.proto
    moderator_commands.proto
    move_card_to_zone.proto
    response_activate.proto
    response_adjust_mod.proto
    response_ban_history.proto
    response_deck_download.proto
    response_deck_list.proto
    response_deck_upload.proto
    response_dump_zone.proto
    response_forgotpasswordrequest.proto
    response_get_games_of_user.proto
    response_get_user_info.proto
    response_join_room.proto
    response_list_users.proto
    response_login.proto
    response_password_salt.proto
    response_register.proto
    response_replay_download.proto
    response_replay_list.proto
    response_viewlog_history.proto
    response_warn_history.proto
    response_warn_list.proto
    response_get_admin_notes.proto
    response.proto
    room_commands.proto
    room_event.proto
    serverinfo_arrow.proto
    serverinfo_ban.proto
    serverinfo_cardcounter.proto
    serverinfo_card.proto
    serverinfo_chat_message.proto
    serverinfo_counter.proto
    serverinfo_deckstorage.proto
    serverinfo_game.proto
    serverinfo_gametype.proto
    serverinfo_playerping.proto
    serverinfo_playerproperties.proto
    serverinfo_player.proto
    serverinfo_replay.proto
    serverinfo_replay_match.proto
    serverinfo_room.proto
    serverinfo_user.proto
    serverinfo_warning.proto
    serverinfo_zone.proto
    server_message.proto
    session_commands.proto
    session_event.proto
)

if(${Protobuf_VERSION} VERSION_LESS "3.21.0.0")
  message(STATUS "Using Protobuf Legacy Mode")
  include_directories(${PROTOBUF_INCLUDE_DIRS})
  include_directories(${CMAKE_CURRENT_BINARY_DIR})
  protobuf_generate_cpp(PROTO_SRCS PROTO_HDRS ${PROTO_FILES})

  add_library(cockatrice_protocol ${PROTO_SRCS} ${PROTO_HDRS})
  set(cockatrice_protocol_LIBS ${PROTOBUF_LIBRARIES})
  if(UNIX)
    set(cockatrice_protocol_LIBS ${cockatrice_protocol_LIBS} -lpthread)
  endif(UNIX)
  target_link_libraries(cockatrice_protocol ${cockatrice_protocol_LIBS})

  # ubuntu uses an outdated package for protobuf, 3.1.0 is required
  if(${Protobuf_VERSION} VERSION_LESS "3.1.0")
    # remove unused parameter and misleading indentation warnings when compiling to avoid errors
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Wno-unused-parameter -Wno-misleading-indentation")
    message(WARNING "Older protobuf version found (${Protobuf_VERSION} < 3.1.0), "
                    "disabled the warnings 'unused-parameter' and 'misleading-indentation' for protobuf generated code "
                    "to avoid compilation errors."
    )
  endif()
else()
  add_library(cockatrice_protocol ${PROTO_FILES})
  target_link_libraries(cockatrice_protocol PUBLIC protobuf::libprotobuf)
  set(PROTO_BINARY_DIR "${CMAKE_CURRENT_BINARY_DIR}")
  target_include_directories(cockatrice_protocol PUBLIC "${PROTOBUF_INCLUDE_DIRS}")

  protobuf_generate(
    TARGET cockatrice_protocol IMPORT_DIRS "${CMAKE_CURRENT_LIST_DIR}" PROTOC_OUT_DIR "${PROTO_BINARY_DIR}"
  )
endif()
